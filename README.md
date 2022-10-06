<p align="center">
  <a href="#"><img src="https://readme-typing-svg.herokuapp.com/?center=true&vCenter=true&lines=Construction+Cluster+HA+k3s;"></a>
</p>
<p align="center">
    🚀 Ce playbook construira un cluster HA Kubernetes avec `k3s`, `kube-vip` et `MetalLB` via `ansible`.
</p>
<p align="center">
    <a href="https://github.com/Drack0rr/k3s-ansible#Compatiblité"><img src="https://img.shields.io/badge/How_to_use-%2341454A.svg?style=for-the-badge&logo=target&logoColor=white"> </a>
    <a href="https://github.com/Drack0rr/k3s-ansible#Prérequis"><img src="https://img.shields.io/badge/Monitoring-%2341454A.svg?style=for-the-badge&logo=target&logoColor=white"> </a>
    <a href="https://github.com/Drack0rr/k3s-ansible#Utilisation"><img src="https://img.shields.io/badge/Auto_update-%2341454A.svg?style=for-the-badge&logo=target&logoColor=white"> </a>
    <br /><br />

# 📖  Compatibilité

Créez un cluster Kubernetes à l'aide d'Ansible avec k3s. L'objectif est d'installer facilement un cluster HA Kubernetes sur des machines exécutant :

- [x] Debian (testé sur la version  11)
- [x] Ubuntu (testé sur la version  22.04)
- [x] Rocky (testé sur la version  9)

Sur l'architecture du processeur :

- [X] x64
- [X] arm64
- [X] armhf

# ✅  Prérequis

- L'environnement de déploiement doit avoir Ansible 2.4.0+.
  
- [`netaddr` package](https://pypi.org/project/netaddr/)doit être disponible pour Ansible. Si vous avez installé Ansible via apt, cela est déjà pris en charge. Si vous avez installé Ansible via `pip`, assurez-vous de l'installer `netaddr` dans l'environnement virtuel respectif.
  
- `serveur` et `agent` les nœuds doivent avoir un accès SSH sans mot de passe, sinon vous pouvez fournir des arguments pour fournir des informations d'identification `--ask-pass --ask-become-pass` à chaque commande.

- Vous devrez également installer les collections utilisées par ce playbook en exécutant `ansible-galaxy collection install -r ./collections/requirements.yml`

# 🚀  Utilisation

## 🍴  Préparation

Créez d'abord un nouveau répertoire basé sur le répertoire `exemple` dans le répertoire `inventaire` :

```bash
cp -R inventaire/exemple inventaire/mon-cluster
```

Deuxièmement, modifiez `inventaire/mon-cluster/hosts.ini` pour faire correspondre les informations système recueillies ci-dessus.

Par exemple :

```ini
[master]
192.168.40.81
192.168.40.82
192.168.40.83

[node]
192.168.40.84
192.168.40.85

[k3s_cluster:children]
master
node
```

Si plusieurs hôtes se trouvent dans le groupe maître, le playbook configurera automatiquement k3s en [mode HA avec etcd](https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/).

Cela nécessite au moins la version k3s, `1.19.1` mais la version est configurable à l'aide de la variable `k3s_version`.

Si nécessaire, vous pouvez également modifier `inventaire/mon-cluster/group_vars/all.yml` pour correspondre à votre environnement.

## ☸️  Créer un cluster

Démarrez le provisionnement du cluster à l'aide de la commande suivante :

```bash
ansible-playbook playbook.yml -i inventaire/mon-cluster/hosts.ini
```

Après le déploiement, le control plane sera accessible via l'adresse IP virtuelle qui est définie dans l'inventaire/group_vars/all.yml comme `apiserver_endpoint`.

## 🔥  Supprimer le cluster k3s

```bash
ansible-playbook reset.yml -i inventaire/mon-cluster/hosts.ini
```

> Vous devez également redémarrer ces nœuds car le VIP n'est pas détruit
{.is-warning}

## ⚙️  Config Kube

Pour copier votre `config kube`, afin de pouvoir accéder à votre cluster Kubernetes , exécutez :

```bash
scp admin-local@master_ip:~/.kube/config ~/.kube/config
```

## 🔨  Tester votre cluster

Voir les commandes [ici](https://wiki.rotthier.fr/Conteneurisation/Kubernetes/Installation).

