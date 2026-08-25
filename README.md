# Vitrine Rabaska OS — GitHub Pages + domaine `rabaskaos.com`

Ce dossier est le **prototype** du site public. Il ne contient aucune ISO,
aucune clé, aucun script d’installation.

**Ne publie JAMAIS ce dépôt BorealOS.** GitHub Pages d’un dépôt gratuit
n’accepte un site que si le dépôt est **public**. Un dépôt public de la
recette = tu donnes le produit. D’où un **second dépôt**, vide, qui n’a
que ces fichiers.

**Domaine public :** `https://rabaskaos.com`  
**Ancien (rediriger) :** `https://rabaskaos.mrservices.ca`  
**Dépôt Pages :** `perigon99/rabaskaos-site`

## Ce qui reste privé (famille à nourrir)

| Public (ce site) | Privé (ce repo + clés) |
|---|---|
| HTML de présentation | `boreal-setup.sh`, ISO, `boreal-*-rs/` |
| Phrase « pas de téléchargement » | Paquets `.deb` signés (canal `apt.mrservices.ca` seulement) |
| Page Droits | Clé GPG secrète, passphrase |

Les machines déjà vendues se mettent à jour par `https://apt.mrservices.ca`
(Origin/Label **`Boreal`**, paquet **`boreal-desktop` 3.6.1**). Ça ne passe
**pas** par cette vitrine.

## Procédure DNS — domaine `rabaskaos.com` (PM)

Le site est déjà poussé avec `CNAME` = `rabaskaos.com`. Il reste le DNS
chez le registrar / Cloudflare du **`.com`**, puis un clic dans GitHub.

### Chez le registrar / Cloudflare de `rabaskaos.com`

Ouvre la zone DNS du domaine **`rabaskaos.com`** (pas `mrservices.ca`).

**Apex (`rabaskaos.com`) — 4 enregistrements A** (doc GitHub Pages) :

| Type | Nom / Host | Valeur |
|------|------------|--------|
| `A` | `@` (ou vide / `rabaskaos.com`) | `185.199.108.153` |
| `A` | `@` | `185.199.109.153` |
| `A` | `@` | `185.199.110.153` |
| `A` | `@` | `185.199.111.153` |

Optionnel (IPv6) — 4× `AAAA` sur `@` :

- `2606:50c0:8000::153`
- `2606:50c0:8001::153`
- `2606:50c0:8002::153`
- `2606:50c0:8003::153`

**Variante** si le DNS offre `ALIAS` / `ANAME` sur l’apex : un seul
enregistrement `@` → `perigon99.github.io` (à la place des 4 A).

**Sous-domaine `www` — 1 CNAME :**

| Type | Nom | Valeur |
|------|-----|--------|
| `CNAME` | `www` | `perigon99.github.io` |

**Proxy Cloudflare :** nuage **gris** (DNS only) sur tous ces
enregistrements. Si le nuage est orange, GitHub n’émet pas le certificat
HTTPS Let’s Encrypt.

Supprime tout ancien A/CNAME qui pointe ailleurs (parking, « coming soon », etc.).

### Dans GitHub (dépôt `rabaskaos-site`)

1. **Settings → Pages**
2. Source : branche `main`, dossier `/` (racine)
3. **Custom domain** : `rabaskaos.com` → Save  
   (le fichier `CNAME` du dépôt doit contenir exactement `rabaskaos.com`)
4. Attendre **DNS check successful** (quelques minutes à quelques heures)
5. Cocher **Enforce HTTPS**

Vérif :

```bash
curl -fsSI https://rabaskaos.com | head
curl -fsSI https://www.rabaskaos.com | head
```

Tu dois voir `200` (ou une redirection vers l’apex) et un certificat GitHub.

### Ancien domaine `rabaskaos.mrservices.ca` (ne pas perdre l’indexation)

Dans la zone DNS de **`mrservices.ca`** (Cloudflare) :

1. Garde (ou crée) le **CNAME** `rabaskaos` → `perigon99.github.io`, nuage **gris**
2. Dans GitHub Pages, tu ne peux avoir qu’**un** custom domain principal
   (`rabaskaos.com`). Pour rediriger l’ancien nom :
   - **Option simple (recommandée)** : dans Cloudflare, règle
     **Redirect Rules** (ou Page Rules) :
     `https://rabaskaos.mrservices.ca/*` → `https://rabaskaos.com/$1`
     (301 Permanent Redirect)
   - Laisse le CNAME actif le temps que la règle prenne effet ; ensuite
     l’ancien URL envoie les visiteurs et Google vers le `.com`

Si Access Zero Trust couvre `*.mrservices.ca`, garde un **Bypass** pour
`rabaskaos.mrservices.ca` tant que la redirection est active.

### Google Search Console (première fois — simple)

1. Va sur [Google Search Console](https://search.google.com/search-console)
2. **Ajouter une propriété** → type **Préfixe d’URL** → `https://rabaskaos.com`
3. Vérifie la propriété (fichier HTML déjà sur le site :
   `googled667a535c39086d8.html`, ou balise / DNS selon ce que Google propose)
4. **Sitemaps** → soumettre : `https://rabaskaos.com/sitemap.xml`
5. Si l’ancienne propriété `https://rabaskaos.mrservices.ca` existe :
   **Paramètres → Changement d’adresse** → indiquer `https://rabaskaos.com`
   (après que la redirection 301 fonctionne)

## Publier une retouche du prototype

```bash
# Depuis une machine avec git
git clone git@github.com:perigon99/rabaskaos-site.git
cd rabaskaos-site
# Windows : C:\Users\perig\BorealOS\site\
cp -r /mnt/c/Users/perig/BorealOS/site/. .
# Exclure les scripts de build locaux s’ils ne doivent pas être publics :
# rm -f _i18n_build.py _i18n_build2.py
# Le fichier CNAME doit rester exactement : rabaskaos.com
git add .
git commit -m "Mise à jour vitrine Rabaska OS"
git push
```

Vérifie qu’il n’y a **aucun** `.sh`, `.deb`, `Cargo.toml`, ISO.

Tu modifies `BorealOS/site/` ici (privé), puis tu recopies vers
`rabaskaos-site` et tu pousses. Jamais l’inverse, jamais un submodule
vers ce repo.

## Anti-détournement (recommandé)

GitHub → Settings du **compte** → Pages → Verify domain : un TXT
`_github-pages-challenge-perigon99` à coller dans le DNS de `rabaskaos.com`.
Empêche un tiers de coller ton domaine sur son Pages.

## Ce que cette page ne fait pas

- Elle ne sert **pas** l’ISO (limite GitHub ~100 Mo / fichier, et ce serait
  donner le produit).
- Elle ne remplace **pas** `apt.mrservices.ca`.
- Le logo « R » terracotta est celui de la 3.6.1 (canal apt). Cette
  vitrine ne publie pas le paquet.
