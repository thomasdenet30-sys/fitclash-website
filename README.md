# Site Fit Clash — fittclash.com

Site statique (HTML/CSS pur, **zéro dépendance, zéro build**) pour les URLs obligatoires App Store Connect.

## Pages
| URL | Fichier |
|---|---|
| `https://fittclash.com/` | `index.html` |
| `https://fittclash.com/support` | `support/index.html` |
| `https://fittclash.com/privacy` | `privacy/index.html` |
| `https://fittclash.com/terms` | `terms/index.html` |

Les URLs « propres » (`/support`, `/privacy`, `/terms`) fonctionnent automatiquement grâce à la structure en dossiers (`dossier/index.html`) — aucune config de réécriture nécessaire sur Cloudflare Pages **ni** Vercel.

## Contenu du dossier
```
index.html
support/index.html
privacy/index.html
terms/index.html
styles.css        (DA ON AIR partagée)
anton.ttf         (police d'affichage embarquée, aucune requête externe)
favicon.png
robots.txt
sitemap.xml
app-ads.txt       (validation AdMob — voir plus bas)
```

## app-ads.txt

`https://fittclash.com/app-ads.txt` déclare que Google AdMob a le droit de
vendre l'inventaire publicitaire de l'app. Google le lit à la **racine du
domaine indiqué sur la fiche App Store** : tant qu'il manque, AdMob laisse
l'app « non validée » et bride la diffusion.

L'identifiant `pub-7977656042089301` est celui du compte AdMob ; il est public
par nature — c'est tout l'objet du fichier. Toute modification doit être
redéployée sur Cloudflare Pages pour être prise en compte, et Google peut
mettre jusqu'à 24 h à le relire.

## Déployer

Le site est un **Worker Cloudflare « assets-only »** nommé `fitclash-website`
(compte `ecom59657@gmail.com`, zone `fittclash.com`) — pas un projet Pages,
malgré ce que laissait entendre l'ancienne version de ce README. La config vit
dans `wrangler.jsonc`, donc un déploiement tient en une commande :

```bash
npx wrangler deploy
```

Deux pièges rencontrés le 2026-08-28 :

- **`.assetsignore` est obligatoire.** Sans lui, `wrangler` téléverse aussi les
  dossiers cachés : `.git` était servi sur `fittclash.com`, historique du dépôt
  compris. Le fichier l'exclut, avec `.gitignore`, `.wrangler` et la config.
- **Le cache `.wrangler/cache/` retient le dernier compte utilisé.** Après un
  `wrangler login` sur un autre compte, `deploy` échoue en
  « Authentication error [code: 10000] » même une fois reconnecté au bon
  compte. Supprimer `.wrangler/cache` règle le problème.

Le dépôt n'est **pas** connecté à Cloudflare : pousser sur GitHub ne déploie
rien, il faut lancer `wrangler deploy`.

## Déployer sur Vercel (alternative)
1. `vercel` (CLI) dans le dossier, ou **Add New → Project → Import**.
2. **Framework Preset** : `Other` · **Build Command** : *(vide)* · **Output Directory** : `./` (racine).
3. **Settings → Domains → `fittclash.com`** et suis les instructions DNS.

## Vérifier localement
```bash
cd fitclash-website
python3 -m http.server 8080
# puis ouvre http://localhost:8080/  (et /support/  /privacy/  /terms/)
```

## Après déploiement — vérifier les 4 URLs
- https://fittclash.com
- https://fittclash.com/support
- https://fittclash.com/privacy
- https://fittclash.com/terms

Renseigne ensuite dans **App Store Connect** :
- URL marketing : `https://fittclash.com`
- URL d'assistance : `https://fittclash.com/support`
- URL de confidentialité : `https://fittclash.com/privacy`
- (Conditions / EULA) : `https://fittclash.com/terms`

> Pense à créer l'adresse **contact@fittclash.com** (redirection email chez ton registrar / Cloudflare Email Routing) — elle est citée sur le site et dans l'app.
