# CNAME – Canonical Name Record

Comprendre et configurer les enregistrements CNAME pour rediriger tes domaines comme un pro.

---

## C'est quoi un CNAME ?

!!! note "Définition simple"
    Un **CNAME** (Canonical Name) est un type d'enregistrement DNS qui permet de faire pointer un nom de domaine vers **un autre nom de domaine** (et non vers une adresse IP).

### Analogie pour comprendre

Imagine le système postal :

| Concept | Analogie |
|---------|----------|
| **Enregistrement A** | Ton adresse physique exacte (123 rue Example) |
| **Enregistrement CNAME** | "Faire suivre le courrier" vers une autre adresse |

!!! example "Exemple concret"
    Tu veux que `docs.monsite.com` affiche le même contenu que `rayano-h.github.io`.
    
    Au lieu de dupliquer le serveur, tu crées un CNAME qui dit :
    
    **"Tout ce qui arrive sur `docs.monsite.com`, redirige-le vers `rayano-h.github.io`"**

---

## Comment ça fonctionne ?

Quand un utilisateur tape `docs.monsite.com` dans son navigateur :

1. **Le navigateur** demande au DNS : "C'est quoi l'IP de `docs.monsite.com` ?"
2. **Le DNS** trouve le CNAME et répond : "Va voir `rayano-h.github.io`"
3. **Le DNS** résout ensuite `rayano-h.github.io` → `185.199.108.153`
4. **Le navigateur** se connecte à cette IP

!!! info "Point clé"
    Le CNAME est une **indirection**. Il ne donne pas directement l'IP, il pointe vers un autre nom qui, lui, sera résolu en IP.

---

## CNAME vs autres enregistrements DNS

| Type | Pointe vers | Exemple | Usage |
|------|-------------|---------|-------|
| **A** | Adresse IPv4 | `192.168.1.1` | Serveur avec IP fixe |
| **AAAA** | Adresse IPv6 | `2001:db8::1` | Serveur avec IPv6 |
| **CNAME** | Autre domaine | `example.github.io` | Alias, redirection |
| **MX** | Serveur mail | `mail.google.com` | Emails |
| **TXT** | Texte libre | `v=spf1...` | Vérification, SPF |

---

## Cas pratiques

### Cas 1 : GitHub Pages avec domaine personnalisé

**Objectif** : `docs.monsite.com` → ton site GitHub Pages

**Configuration DNS :**

| Type | Nom | Cible | TTL |
|------|-----|-------|-----|
| CNAME | docs | rayano-h.github.io | 3600 |

**Fichier CNAME dans ton repo :**

!!! example "Fichier CNAME dans ton repo"
    ```
    docs.monsite.com
    ```

### Cas 2 : Redirection www vers domaine principal

**Objectif** : `www.monsite.com` → `monsite.com`

**Configuration DNS :**

| Type | Nom | Cible | TTL |
|------|-----|-------|-----|
| CNAME | www | monsite.com | 3600 |

---

### Cas 3 : Pointer vers un CDN (Cloudflare, AWS CloudFront)

**Objectif** : `static.monsite.com` → CDN pour les assets

**Configuration DNS :**

| Type | Nom | Cible | TTL |
|------|-----|-------|-----|
| CNAME | static | d1234abcd.cloudfront.net | 3600 |

---

## Erreurs courantes et solutions

### Erreur 1 : CNAME sur domaine racine (apex)

!!! danger "Impossible"
    Tu **ne peux pas** créer un CNAME sur un domaine racine (apex).

| Valide | Invalide |
|--------|----------|
| `www.monsite.com` → CNAME | `monsite.com` → CNAME |
| `docs.monsite.com` → CNAME | `@` → CNAME |

**Pourquoi ?** Les standards DNS interdisent un CNAME à la racine car il entrerait en conflit avec les records SOA et NS obligatoires.

!!! success "Solutions"
    1. **ALIAS record** (si ton provider le supporte : Cloudflare, Route53)
    2. **A records** vers les IPs directement
    3. **Redirection HTTP** depuis la racine vers www

---

### Erreur 2 : CNAME avec autre enregistrement

!!! danger "Interdit"
    Un CNAME **ne peut pas coexister** avec d'autres enregistrements sur le même nom.

**Incorrect :**

| Type | Nom | Valeur |
|------|-----|--------|
| CNAME | www | example.com |
| TXT | www | verification=123 |

**Correct :** Utilise le domaine cible pour les autres records.

---

### Erreur 3 : Boucle de CNAME

!!! danger "Éviter les boucles"
    Ne crée jamais de boucle où A pointe vers B qui pointe vers A.

---

## Commandes utiles pour diagnostiquer

### Vérifier un CNAME existant

```bash
# Avec dig
dig docs.monsite.com CNAME

# Avec nslookup
nslookup -type=CNAME docs.monsite.com

# Avec host
host -t CNAME docs.monsite.com