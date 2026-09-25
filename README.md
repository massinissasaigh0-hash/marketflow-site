# Site public Marketflow — GitHub Pages

Deux pages statiques, prêtes à publier gratuitement sur GitHub Pages :
la **politique de confidentialité** (exigée par eBay) et le **relais de callback OAuth**.

## Publication (procédure exacte)

```bash
# 1. Crée un dépôt GitHub public nommé "marketflow-site" sur https://github.com/new

# 2. Depuis le dossier "site" de ce projet :
cd site
git init
git add .
git commit -m "Public site: privacy policy + OAuth callback relay"
git branch -M main
git remote add origin https://github.com/<TON-COMPTE>/marketflow-site.git
git push -u origin main

# 3. Sur GitHub : Settings → Pages → Source : "Deploy from a branch" → main / (root) → Save
#    L'URL devient active après ~1 minute.
```

## URLs obtenues (à remplacer par ton compte GitHub)

- Politique de confidentialité : `https://<TON-COMPTE>.github.io/marketflow-site/privacy/`
- Relais callback OAuth : `https://<TON-COMPTE>.github.io/marketflow-site/callback/`

## À déclarer dans le portail développeur eBay (developer.ebay.com)

1. **Application Settings → your key set → Auth's accepted URL (RuName)** :
   - « Auth accepted URL » = `https://<TON-COMPTE>.github.io/marketflow-site/callback/index.html`
   - (GitHub Pages sert `index.html` automatiquement ; mettre l'URL complète est le plus sûr)
2. **Privacy policy URL** (formulaire de l'application) :
   - `https://<TON-COMPTE>.github.io/marketflow-site/privacy/`
3. Copie le **RuName** affiché (format `--nom-choisi--`) dans `.env` :
   - `EBAY_RUNAME=<le RuName>`
   - `WEB_ORIGIN=https://<TON-COMPTE>.github.io` (autorisé par le CORS du bot)
   - `PUBLIC_BASE_URL` reste facultatif (utile seulement si tu exposes plus tard un vrai domaine).

## Flux OAuth complet avec ce dispositif

1. Bouton « Connecter eBay » dans l'application → `GET /auth/ebay/start` → redirection vers le consentement eBay.
2. Après « Accepter », eBay redirige vers la **page relais GitHub Pages** (`?code=...`).
3. La page pré-remplit le code ; l'utilisateur clique « Terminer la connexion ».
4. La page appelle `POST <URL_DU_BOT>/api/ebay/oauth/exchange-code` (CORS limité à WEB_ORIGIN).
5. Le bot échange le code contre les jetons et les **chiffre localement** — les secrets OAuth ne quittent jamais ta machine.

Alternative si tu obtiens plus tard un domaine : déclare plutôt
`https://<TON-DOMAINE>/auth/ebay/callback` (callback automatique déjà implémenté côté bot)
et laisse la page relais comme secours.
