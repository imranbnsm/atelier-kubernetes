# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :**Imrân Benessam
**Groupe :**RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   >3 nœuds s'affichent : 1 nœud control-plane (`k3d-lab-server-0`) qui pilote le cluster et héberge l'API Server, et 2 nœuds workers (`k3d-lab-agent-0` et `k3d-lab-agent-1`) qui exécutent les charges de travail applicatives.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   >`docker ps` liste les conteneurs Docker en cours d'exécution. Concrètement dans k3d, chaque nœud correspond à un conteneur Docker distinct qui exécute une instance de k3s (une distribution Kubernetes légère).

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   >Un Deployment, un ReplicaSet et un Pod (ainsi qu'un Service associé suite à la commande `expose`).
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >Le pod supprimé a immédiatement disparu et un nouveau pod a été recréé automatiquement avec un nom différent. C'est le principe d'auto-réparation du Deployment, qui garantit que l'état réel correspond à l'état désiré.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >Non, le message a disparu. Les conteneurs étant éphémères, toute modification effectuée à chaud à l'intérieur d'un conteneur est perdue dès sa destruction et sa recréation.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >Le tunnel était lié au pod ou au Service ciblant un pod qui a été détruit. Lorsque la ressource cible a changé, le port-forward s'est coupé.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >`kubectl create` est impératif et échoue si la ressource existe déjà. `kubectl apply` est déclaratif : il applique l'état décrit dans un manifeste YAML, créant la ressource si elle n'existe pas ou la mettant à jour si elle a changé.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >Le selector permet au Service d'identifier quels pods cibler en fonction de leurs labels. S'il ne correspond à aucun pod, le Service ne transmettra aucun trafic (les endpoints associés seront vides).

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```@imranbnsm ➜ /workspaces/atelier-kubernetes (main) $ kubectl get pods -o wide -w
NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE               NOMINATED NODE   READINESS GATES
site-75775484b-4bkkg   1/1     Running   0          67s   10.42.2.6   k3d-lab-server-0   <none>           <none>
site-75775484b-65tkx   1/1     Running   0          74s   10.42.1.7   k3d-lab-agent-1    <none>           <none>
web-68d995574f-nw42f   1/1     Running   0          13m   10.42.1.6   k3d-lab-agent-1    <none>           <none>
site-75775484b-wcp4t   1/1     Running             0          6s    10.42.0.5   k3d-lab-agent-0    <none>           <none>
site-5cc664db9c-gzrhc   1/1     Running             0          5s    10.42.2.7   k3d-lab-server-0   <none>      
      <none>
site-5cc664db9c-sgcd5   1/1     Running             0          8s     10.42.0.6   k3d-lab-agent-0    <none>           <none>

site-5cc664db9c-lpdj6   1/1     Running             0          8s     10.42.1.8   k3d-lab-agent-1    <none>           <none>


   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >Le pod affichait un statut ImagePullBackOff / ErrImagePull. La commande kubectl describe pod (ou kubectl logs) a permis de visualiser l'erreur d'extraction de l'image inexistante dans la section Events.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >Oui, le site restait accessible car le déploiement utilise une mise à jour progressive (rolling update). Les anciens pods valides continuaient de traiter le trafic tant que les nouveaux n'étaient pas opérationnels.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
