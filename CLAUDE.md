# Chatbase Clone - Backend API

## 📋 Vue d'ensemble

Clone de Chatbase développé avec FastAPI et PostgreSQL, utilisant exclusivement l'API Anthropic Claude pour les fonctionnalités IA.

### ✅ Fonctionnalités implémentées

- **Upload de documents** : Integration avec Anthropic Files API
- **Agents IA personnalisables** : Prompts, modèles, paramètres configurables
- **Mode strict documentaire** : Force les réponses exclusivement basées sur documents
- **Fallback personnalisable** : Messages personnalisés quand info non trouvée
- **Conversations avec contexte** : Chat avec documents comme source de contexte
- **Prompt dynamique** : Construction intelligente selon configuration agent
- **Gestion des tokens et coûts** : Calcul automatique des coûts Anthropic
- **Base de données async** : PostgreSQL avec SQLAlchemy async

## 🏗️ Architecture

```
app/
├── api/           # Routes FastAPI
│   ├── chat.py    # Conversations et messages
│   ├── documents.py # Upload et gestion documents
│   └── agents.py  # Gestion des agents IA
├── services/      # Logique métier
│   ├── anthropic_service.py  # Interface API Anthropic
│   ├── document_service.py   # Gestion documents
│   └── chat_service.py       # Conversations avec contexte
├── models/        # Modèles SQLAlchemy
├── schemas/       # Schémas Pydantic
└── database.py    # Configuration DB
```

## 🔧 Services principaux

### AnthropicService
- **Upload de fichiers** : `upload_file()`
- **Chat avec documents** : `chat_with_documents()`
- **Chat simple** : `chat_simple()`
- **Calcul des coûts** : `calculate_estimated_cost()`

### DocumentService  
- **Upload vers agent** : `upload_document()`
- **Récupération par agent** : `get_documents_by_agent()`
- **File IDs pour contexte** : `get_file_ids_for_agent()`

### ChatService
- **Création conversations** : `create_conversation()`
- **Envoi messages** : `send_message()` avec mode strict
- **Prompt dynamique** : Construction selon `strict_document_mode`
- **Fallback personnalisé** : Utilise `fallback_response` de l'agent
- **Historique** : `get_conversation_history()`

## 🌐 API Endpoints

### Documents
- `POST /api/v1/documents/upload` - Upload document
- `GET /api/v1/documents/agent/{agent_id}` - Documents d'un agent

### Chat  
- `POST /api/v1/chat/conversations` - Créer conversation
- `POST /api/v1/chat/send` - Envoyer message
- `GET /api/v1/chat/conversations/{id}/history` - Historique

### Agents
- `POST /api/v1/agents/` - Créer agent avec mode strict
- `GET /api/v1/agents` - Lister agents
- `GET /api/v1/agents/{id}` - Détails d'un agent
- `PUT /api/v1/agents/{id}` - Modifier agent
- `DELETE /api/v1/agents/{id}` - Supprimer agent

## 🔑 Configuration requise

### Variables d'environnement
```bash
ANTHROPIC_API_KEY=sk-ant-...
DATABASE_URL=postgresql+asyncpg://user:pass@host:port/db
```

### Modèles supportés
- `claude-3-5-sonnet-20241022` (recommandé)
- `claude-3-haiku-20240307` (rapide, économique)
- `claude-3-opus-20240229` (plus puissant)

## 💰 Tarification (approximative)

| Modèle | Input (1K tokens) | Output (1K tokens) |
|--------|-------------------|-------------------|
| Sonnet | $0.003 | $0.015 |
| Haiku | $0.00025 | $0.00125 |
| Opus | $0.015 | $0.075 |

## 🧪 Tests effectués

### ✅ Workflow complet validé
1. **Upload document** : `test_document.txt` → `file_011CSckFW7FrHvGdpvuHv9iu`
2. **Création agent** : Agent Test avec prompt personnalisé
3. **Conversation** : ID `e1e8694c-e24c-4a6a-9ee4-791d952a9cb2`
4. **Message avec contexte** : Question sur modèles Claude
5. **Réponse intelligente** : 3 modèles identifiés correctement
6. **Métriques** : 679+93=772 tokens, $0.003432

### ✅ Mode strict documentaire validé
1. **Agent sans documents** : Réponses générales autorisées
2. **Agent avec documents + question pertinente** : Réponse basée sur documents
3. **Agent avec documents + question hors sujet** : Fallback personnalisé utilisé
4. **Création agent avec mode strict** : Nouveaux champs fonctionnels
5. **API endpoints** : Tous les endpoints retournent les nouveaux champs

### 🐛 Bugs corrigés
- **❌ greenlet_spawn error** : Mélange async/sync dans chat_service
- **✅ Solution** : Simplification requêtes SQL post-commit
- **❌ Import errors** : Types Anthropic non importables  
- **✅ Solution** : Utilisation type `Any`
- **❌ API Files syntax** : Ancienne syntaxe
- **✅ Solution** : `client.beta.files.upload()`
- **❌ Validation errors** : Nouveaux champs NULL en base
- **✅ Solution** : Mise à jour agents existants avec valeurs par défaut
- **❌ Foreign key violation** : UUID utilisateur inexistant
- **✅ Solution** : Utilisation UUID utilisateur existant

## 🚀 Démarrage

```bash
# Installation
pip install -r requirements.txt

# Migrations
alembic upgrade head

# Démarrage serveur
uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```

## 📁 Types de fichiers supportés

- **Texte** : .txt, .md, .html, .csv, .json
- **Documents** : .pdf, .doc, .docx  
- **Images** : .jpg, .jpeg, .png, .gif, .webp

## 🔗 Intégration Anthropic

### Files API (Beta)
```python
client.beta.files.upload(
    file=(filename, file_content, mime_type)
)
```

### Messages API avec documents
```python
client.beta.messages.create(
    model="claude-3-5-sonnet-20241022",
    messages=[{
        "role": "user", 
        "content": [
            {"type": "document", "source": {"type": "file", "file_id": "..."}},
            {"type": "text", "text": "Question utilisateur"}
        ]
    }]
)
```

## 🆕 Nouveautés - Mode Strict Documentaire

### Configuration des agents
```python
# Champs ajoutés au modèle Agent
strict_document_mode: bool = True  # Force usage exclusif des documents
fallback_response: str = "Message personnalisé si info non trouvée"
```

### Comportement dynamique du prompt
- **Sans documents** : Mode normal, connaissances générales autorisées
- **Avec documents + mode strict OFF** : Priorité aux documents, connaissances générales en complément
- **Avec documents + mode strict ON** : Réponses UNIQUEMENT basées sur les documents
- **Info non trouvée** : Utilise le `fallback_response` personnalisé de l'agent

### Exemple d'utilisation API
```json
{
  "name": "Assistant Financier",
  "strict_document_mode": true,
  "fallback_response": "Cette information financière n'est pas disponible dans mes documents."
}
```

## 📊 État du projet

- **Backend** : ✅ Fonctionnel
- **API Documentation** : ✅ Swagger UI disponible
- **Tests intégration** : ✅ Validés
- **Mode strict documentaire** : ✅ Implémenté et testé
- **Frontend** : ⏳ À développer
- **Auth système** : ⏳ À implémenter
- **Routes agents** : ✅ Complètes

---

**Dernière mise à jour** : 30 août 2025  
**Version** : 1.1.0  
**Status** : ✅ Opérationnel - Mode strict documentaire intégré