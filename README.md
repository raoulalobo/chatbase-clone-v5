# 🤖 Chatbase Clone - Backend API

Un clone de Chatbase développé avec **FastAPI** et **PostgreSQL**, utilisant exclusivement l'**API Anthropic Claude** pour les fonctionnalités IA.

## ✨ Fonctionnalités

- 📁 **Upload de documents** avec intégration Anthropic Files API
- 🤖 **Agents IA personnalisables** (prompts, modèles, paramètres)
- 🔒 **Mode strict documentaire** - Réponses basées uniquement sur documents
- 💬 **Conversations intelligentes** avec contexte documentaire
- 💰 **Calcul automatique des coûts** et gestion des tokens
- ⚡ **Architecture async** complète avec performance optimisée

## 🆕 Nouveautés v1.1.0

### Mode Strict Documentaire
- **Contrôle précis** : Force les agents à répondre uniquement à partir des documents fournis
- **Fallback personnalisable** : Messages personnalisés quand l'information n'est pas trouvée
- **Prompt dynamique** : Construction intelligente selon la configuration de l'agent

```python
# Configuration d'un agent avec mode strict
{
  "name": "Assistant Financier",
  "strict_document_mode": true,
  "fallback_response": "Cette information financière n'est pas disponible dans mes documents."
}
```

## 🚀 Démarrage rapide

### Prérequis
- Python 3.8+
- PostgreSQL
- Clé API Anthropic

### Installation

```bash
# Cloner le repository
git clone https://github.com/raoulalobo/chatbase-clone-v5.git
cd chatbase-clone-v5

# Créer environnement virtuel
python -m venv venv
source venv/bin/activate  # Linux/Mac

# Installer les dépendances
pip install -r requirements.txt

# Configuration
cp .env.example .env
# Éditer .env avec vos clés
```

### Configuration `.env`

```bash
# API Anthropic
ANTHROPIC_API_KEY=sk-ant-your-key-here

# Base de données
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/chatbase_clone

# Application
ENVIRONMENT=development
DEBUG=True
```

### Démarrage

```bash
# Migrations base de données
alembic upgrade head

# Démarrage serveur de développement
uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```

### 📖 Documentation API
- **Swagger UI** : http://127.0.0.1:8000/docs
- **ReDoc** : http://127.0.0.1:8000/redoc

## 📊 Endpoints principaux

### 🤖 Agents IA
- `POST /api/v1/agents/` - Créer agent avec mode strict
- `GET /api/v1/agents` - Lister agents
- `GET /api/v1/agents/{id}` - Détails d'un agent
- `PUT /api/v1/agents/{id}` - Modifier agent
- `DELETE /api/v1/agents/{id}` - Supprimer agent

### 📁 Documents
- `POST /api/v1/documents/upload` - Upload document
- `GET /api/v1/documents/agent/{agent_id}` - Documents d'un agent

### 💬 Chat
- `POST /api/v1/chat/conversations` - Créer conversation
- `POST /api/v1/chat/send` - Envoyer message avec contexte
- `GET /api/v1/chat/conversations/{id}/history` - Historique

## 🎯 Modèles supportés

| Modèle | Usage | Prix Input | Prix Output |
|--------|-------|------------|-------------|
| **claude-3-5-sonnet-20241022** | Recommandé | $0.003/1K | $0.015/1K |
| **claude-3-haiku-20240307** | Rapide & économique | $0.00025/1K | $0.00125/1K |
| **claude-3-opus-20240229** | Le plus puissant | $0.015/1K | $0.075/1K |

## 🧪 Tests validés

✅ **Workflow complet testé** :
1. Upload document → Conversation → Chat avec contexte
2. Mode strict activé/désactivé selon présence documents
3. Fallback personnalisé quand information non trouvée
4. API agents CRUD complète avec nouveaux champs

## 📚 Documentation complète

Voir [CLAUDE.md](./CLAUDE.md) pour la documentation technique détaillée.

## 📄 Licence

Ce projet est sous licence MIT.

---

**Status** : ✅ Opérationnel - Mode strict documentaire intégré  
**Version** : 1.1.0  
**Dernière mise à jour** : 30 août 2025

🤖 Développé avec [Claude Code](https://claude.ai/code)