# 📌 Guide Ultime pour Debugger les Erreurs via Postman 🚀

## ⚠️ Les Différents Types d'Erreurs

### 🔴 Erreur 500 (Internal Server Error)
💡 **Vérifie les logs du serveur avec la commande :**  
```bash
cmd->wsl-> "cat /var/log/nginx/error.log"
```
➡️ Cela permet d'afficher les erreurs enregistrées par **Nginx**.

---

### 🟠 Erreur 400 et Autres Erreurs de Requête
📌 **Vérifications essentielles :**  
✅ **Headers corrects ?** (`Content-Type`, `Authorization`, etc.)  
✅ **Body bien formaté ?** (`JSON` valide, bon encodage)  
✅ **URL et méthode HTTP correctes ?** (`POST`, `GET`, `PUT`, `DELETE`...)  
✅ **Permissions utilisateur vérifiées ?** (Exemple : `$this->role == "admin"`)

---

## 📂 Organisation des Fichiers (`src/`)

### 🔵 Contrôleurs (`controllers/`)
📌 **À vérifier :**  
✅ **Nom du fichier** → Commence par une **majuscule**.  
✅ **Nom de la classe** → Identique au **nom du fichier** (avec majuscule).  

💡 **Exemple de structure correcte :**  
```php
<?php

namespace App\Controllers; // Vérifie que le namespace correspond au bon répertoire

use App\Controllers\Controller; // On étend la classe Controller
use App\Models\NomDuModel; // Appelle le modèle associé
use App\Utils\{Route, HttpException}; // Route est OBLIGATOIRE pour les requêtes

class NomDuFichierDansLeController extends Controller
{
    #[Route("POST", "/carts")] // Associe la méthode HTTP et l'endpoint
    
    public function addCarts()  
    {
        if ($this->role == "admin") { // Gestion des accès
            return $this->NomdeLaClass->NomdelaMethodeduModel(parametre_de_la_methode); // Appel du modèle
        }
    }
}
```
⚠️ **Attention avec Postman** : L’URL et la méthode HTTP doivent être **exactement** les mêmes que celles définies dans `#[Route("POST", "/carts")]`.

---

### 🛁 Modèles (`models/`)
📌 **À vérifier :**  
✅ **Nom du fichier** → Commence par **une majuscule**.  
✅ **Nom de la classe** → Identique au nom du fichier.  

💡 **Exemple de structure correcte :**  
```php
<?php

namespace App\Models;

use App\Utils\{HttpException}; // Pour lever des exceptions HTTP
use \PDO; // Pour les requêtes SQL avec PDO

class CartsModel extends SqlConnect
{
    // Définition des méthodes pour interagir avec la base de données
}
```
💡 **Important** :  
- `throw new HttpException("Texte de l'erreur", 400);` → Permet d’envoyer une erreur personnalisée à Postman.  
- `$req->fetch(PDO::FETCH_ASSOC);` → Récupère les données sous forme de tableau associatif.

---

## 🔥 Fichier Principal (`index.php`)

💡 **Structure minimale requise :**  
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

## ⚡ Erreurs SQL Courantes

### 🔍 **Erreur sur la Requête SQL**
```php
$query = "SELECT name, description, price, stock, nbr_sales, category, created_at FROM $this->table WHERE name = :name";
$req = $this->db->prepare($query);
$req->execute($data);
```
📌 **Vérifications essentielles :**  
✅ **Le nom de la table `$this->table` est bien écrit** et **existe dans la BDD**.  
✅ **Les noms de colonnes dans la requête SQL correspondent exactement à ceux de la BDD**.  
✅ **Dans Postman, le body contient bien `{ "name": "Text" }`** si un paramètre `:name` est attendu.  

---

### 📌 **Exemples de Requêtes SQL Correctes**

🔍 **Sélectionner des données**
```sql
SELECT Col1, Col2, Col3 FROM NomDeTable WHERE name = :name;
```

✏️ **Modifier des données**
```sql
UPDATE NomDeTable SET modification = :modification WHERE id = :id;
```

➕ **Insérer une ligne**
```sql
INSERT INTO NomDeTable (Col1, Col2, Col3, Col4) VALUES (:Col1, :Col2, :Col3, :Col4);
```

🗑 **Supprimer une ligne**
```sql
DELETE FROM NomDeTable WHERE id = :id AND name = :name;
```

💡 **Lors de l’exécution avec PHP** :
```php
$req->execute([":name" => $data['name']]);
```

---

## 🌟 Conclusion
Si tu appliques tout ça, ton **API sera aussi solide qu'un code de Dieu**. 🔥💪

