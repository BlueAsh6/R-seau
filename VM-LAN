
# 🖧 Configuration réseau & routage des VM (LAN)

## 🎯 Objectif

Permettre aux VM des réseaux internes (ens19, ens20) :

* d’accéder à Internet via ens18
* d’avoir un routage fonctionnel
* avec IP forwarding + NAT iptables

---

## 1️⃣ Vérification et activation de l’IP Forwarding

### Vérifier l’état actuel

```bash
cat /proc/sys/net/ipv4/ip_forward
```

### Si la valeur est `0`

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

Ou via éditeur :

```bash
nano /proc/sys/net/ipv4/ip_forward
```

➡ Remplacer `0` par `1`

---

## 2️⃣ Rendre l’IP Forwarding persistant

### Vérifier la configuration

```bash
cat /etc/sysctl.conf | grep forward
```

### Si aucune ligne n’apparaît

```bash
nano /etc/sysctl.conf
```

Ajouter à la fin du fichier :

```conf
net.ipv4.ip_forward=1
```

Appliquer :

```bash
sysctl -p
```

---

## 3️⃣ Vérification de la configuration réseau

### Interfaces réseau actives

```bash
ip addr show
```

### Table de routage

```bash
ip route
```

### Configuration réseau complète

```bash
cat /etc/network/interfaces
```

---

## 4️⃣ Configuration attendue de `/etc/network/interfaces`

```conf
auto lo
iface lo inet loopback

# Interface WAN
auto ens18
iface ens18 inet static
    address 10.0.0.30
    netmask 255.255.255.0
    gateway 10.0.0.1

# LAN 1
auto ens19
iface ens19 inet static
    address 10.3.10.254
    netmask 255.255.255.0

# LAN 2
auto ens20
iface ens20 inet static
    address 10.3.23.254
    netmask 255.255.255.0
```

### Redémarrage du réseau

```bash
systemctl restart networking
```

Ou interface par interface :

```bash
ifdown ens19 && ifup ens19
ifdown ens20 && ifup ens20
```

---

## 5️⃣ Vérification des règles iptables

### NAT

```bash
iptables -t nat -L -n -v
```

### Forwarding

```bash
iptables -L FORWARD -n -v
```

---

## 6️⃣ Configuration NAT (si aucune règle)

### Activer le masquerading vers le WAN

```bash
iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE
```

---

## 7️⃣ Autoriser le forwarding entre interfaces

### LAN → WAN

```bash
iptables -A FORWARD -i ens19 -o ens18 -j ACCEPT
iptables -A FORWARD -i ens20 -o ens18 -j ACCEPT
```

### WAN → LAN (connexions établies)

```bash
iptables -A FORWARD -i ens18 -o ens19 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i ens18 -o ens20 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

---

## 8️⃣ Vérifications finales

### Règles actives

```bash
iptables -t nat -L -n -v
iptables -L FORWARD -n -v
```

### Tests réseau

```bash
ping 1.1.1.1
ping debian.org
ping 10.3.10.254
ping 10.3.23.254
```

---

## ✅ Résumé

* IP Forwarding activé et persistant
* Interfaces réseau configurées
* NAT actif sur ens18
* Forwarding LAN ↔ WAN autorisé
* Accès réseau fonctionnel pour les VM

