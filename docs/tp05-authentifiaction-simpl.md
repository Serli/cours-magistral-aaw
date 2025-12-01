TP1 — Authentification simple via formulaire
🎯 Objectifs

Implémenter un flux de login minimal.

Contrôler l’accès au front via React.

Backend

Créer une route :

POST /login


Elle doit :

Vérifier un couple login/password codé en dur.

Retourner :

{ "success": true }


en cas de succès.

Retourner :

{ "success": false }


sinon.

Frontend

Créer /login avec un formulaire.

Soumission → requête POST vers /login.

Si succès → redirection vers /todo.

Empêcher /todo d’être accessible si isAuthenticated = false.

Contraintes

Aucune persistance (pas de cookie).

Un refresh = déconnexion.
