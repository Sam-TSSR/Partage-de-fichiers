# Serveur de fichiers Windows 2022 Configuration

### 1. Installation du rôle Serveur de fichiers

- Ouvrez "Gestionnaire de serveur" sur votre serveur Windows 2022.
- Cliquez sur "Ajouter des rôles et des fonctionnalités".
- Sélectionnez "Serveur de fichiers" et suivez les instructions pour installer ce rôle.

### 2. Création d'un dossier "Documents_Entreprise"

- Allez dans le disque C: et créez un dossier appelé "Documents_Entreprise".
- Ce dossier sera partagé avec les autres utilisateurs sur le réseau.

### 3. Création d'un partage SMB

- Ouvrez PowerShell et entrez cette commande pour créer un partage SMB pour le dossier "Documents_Entreprise":
  
  ```powershell
  New-SmbShare -Name "Docs" -Path "C:\Documents_Entreprise" -FullAccess "Everyone"

### 4. Création des sous-dossiers

- Créez trois sous-dossiers dans "Documents_Entreprise" :
 - "RH"
 - "Comptabilité"
 - "Direction"

### 5. Configuration des permissions

- Utilisez PowerShell pour configurer les permissions NTFS et de partage pour chaque groupe :

  - Le groupe "RH" a un accès en lecture/écriture au dossier "RH".
  - Le groupe "Comptabilité" a un accès en lecture/écriture au dossier "Comptabilité".
  - Le groupe "Direction" a un accès en lecture/écriture à tous les dossiers.
  - Tous les utilisateurs du domaine ont un accès en lecture seule au dossier "Documents_Entreprise".

  ```powershell
  Set-Acl -Path "C:\Documents_Entreprise\RH" -AclObject (Get-Acl "C:\Documents_Entreprise\RH" | Add-AccessRule (New-Object System.Security.AccessControl.FileSystemAccessRule("RH", "FullControl",  "ContainerInherit", "ObjectInherit", "None", "Allow")))

### 6. Liste des partages SMB

- Pour lister tous les partages SMB sur le serveur, utilisez la commande suivante dans PowerShell :

  ```powershell
  Get-SmbShare

### 7. Configuration du lecteur réseau sur un client Windows 10

- Sur un poste client Windows 10, configure un lecteur réseau pointant vers ce partage via PowerShell :

  ```powershell
  New-PSDrive -Name "Z" -PSProvider FileSystem -Root "\\WinServer2022\Docs" -Persist

- Vous pouvez vérifier les lecteurs mappés avec cette commande :

  ```powershell
  Get-PSDrive -PSProvider FileSystem

### 8. Test des accès

- Testez l'accès au dossier partagé depuis le client Windows 10 avec différents comptes d'utilisateurs pour vérifier que les permissions sont correctement appliquées.
