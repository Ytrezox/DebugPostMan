# Guide Ultime pour Debugger les Erreurs via Postman 🚀

## ⚠️ Les Différents Types d'Erreurs

### 🔴 Erreur 500 (Internal Server Error)
**Commande pour vérifier les logs :**
```bash
cmd->wsl-> "cat /var/log/nginx/error.log"
```
Cela permet d'afficher les erreurs enregistrées dans les logs du serveur **Nginx**.

### 🟠 Erreur 400 et autres erreurs de requête
**Plusieurs cas à vérifier :**
- **Vérifie les en-têtes de la requête** (Content-Type, Authorization, etc.)
- **Assure-toi que le body est bien formaté** (JSON valide, bon encodage)
- **Confirme que l'URL et la méthode HTTP correspondent à celles définies dans le contrôleur**
- **Vérifie les permissions et les règles d'accès** (exemple : `$this->role == "admin"`)

---

## 📂 Fichiers Sources (`src`)

### 🔵 Contrôleurs (`controllers/`)

**Vérifications essentielles :**
✅ Le **nom du fichier** commence bien par une **majuscule**.
✅ Le **nom de la classe** dans le fichier est **identique au nom du fichier** (majuscule incluse).

**Structure standard d’un contrôleur :**
```php
<?php

namespace App\Controllers; // Vérifie que le namespace correspond au bon répertoire

use App\Controllers\Controller; // On étend la classe Controller
use App\Models\NomDuModel; // Pour appeler le modèle associé
use App\Utils\{Route, HttpException}; // Route est OBLIGATOIRE pour la gestion des requêtes

class NomDuFichierDansLeController extends Controller
{
    #[Route("POST", "/carts")] // Indique la méthode HTTP et le chemin d’accès, DOIT correspondre à Postman
    
    public function addCarts()  
    {
        if ($this->role == "admin") { // Gestion des accès
            return $this->NomdeLaClass->NomdelaMethodeduModel(parametre de la méthode appelée); // Appel du modèle
        }
    }
}
```

⚠️ **Attention avec Postman** : L’URL et la méthode HTTP doivent être **exactement** les mêmes que dans `#[Route("POST", "/carts")]`, sinon la requête échouera.

---

### 🛁 Modèles (`models/`)

**Vérifications essentielles :**
✅ Le **nom du fichier** commence par **une majuscule**.
✅ Le **nom de la classe** correspond **exactement** au nom du fichier.

**Structure standard d’un modèle :**
```php
<?php

namespace App\Models;

use App\Utils\{HttpException}; // Pour lever des exceptions HTTP
use \PDO; // Pour les requêtes SQL avec PDO

class CartsModel extends SqlConnect
{
    // Ici, on définit les méthodes pour interagir avec la base de données
}
```

💡 **Important** :
- `throw new HttpException("Texte de l'erreur", 400);` → Permet d’envoyer une erreur personnalisée à Postman.
- `$req->fetch(PDO::FETCH_ASSOC);` → Permet de récupérer les données sous forme de tableau associatif.

---

## 🔥 Fichier Principal (`index.php`)

**Structure minimale requise :**
```php
<?php

require 'vendor/autoload.php'; // Chargement automatique des dépendances

use App\Router;
use App\Utils\Route;
use App\Controllers\{
    Categories,  // Chargement des contrôleurs
    Orders
};

$router = new Router();

$controllers = [
    Categories::class,
    Orders::class, // Référence aux classes situées dans "namespace App\Controllers"
];
```

💡 **Pourquoi utiliser `::class` ?**  
Il permet de récupérer le **nom complet de la classe avec son namespace**, ce qui évite les erreurs et facilite l'autocomplétion.

---

## 🎯 Résumé des Points Clés

✅ **Erreurs dans Postman :** Vérifie bien **les routes, la méthode HTTP, les permissions et les logs du serveur**.  
✅ **Contrôleurs (`controllers/`) :** Respecte la structure, les namespaces et utilise bien `#[Route("POST", "/carts")]`.  
✅ **Modèles (`models/`) :** Vérifie que les exceptions HTTP sont bien utilisées (`HttpException`).  
✅ **Index.php :** Charge bien tous les **contrôleurs** et respecte l’ordre d’initialisation.  

---

### 🌟 Si tu appliques tout ça, ton API sera aussi solide que le code d’un Dieu. 🔥

