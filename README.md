# VG-Uppgiften – Nätverk & Operativsystem 🖧

Det här repot innehåller min VG-uppgift. Den har **två delar**:

- **Del A:** Hur data reser från min dator ut på internet – steg för steg.
- **Del B:** Hur man ger olika personer olika rättigheter till mappar i **Windows** och **Linux**.



## 📂 Innehåll

| Del | Fil | Mål | Vad handlar det om? |
|---|---|---|---|
| **A** | [Avancerad Nätverksanalys & Trafikflöden](https://github.com/bilaali01/VG-underlag/blob/main/A_Avancerad-N%C3%A4tversanalys-%26-Trafikfl%C3%B6den(M%C3%A5l3).md) | Mål 3 | Ett paket reser från en VM, via gateway och DNS, ut till en server i molnet |
| **B** | [Jämförande OS & Behörighetsanalys](https://github.com/bilaali01/VG-underlag/blob/main/B_J%C3%A4mf%C3%B6rande-OS-%26-beh%C3%B6righetsanalys-(M%C3%A5l2).md) | Mål 2 | Användare, grupper och mapp-behörigheter i Windows och Linux |

---

## 🌐 Del A – Avancerad Nätverksanalys & Trafikflöden

**Frågan:** Vad händer egentligen när jag skriver `example.com` i webbläsaren?

Jag går igenom **OSI-modellens 7 lager** uppifrån och ner, och visar vilket lager i **TCP/IP-modellen** varje del hör till.

| OSI-lager | Vad jag förklarar |
|---|---|
| **7 – Application** | DNS, cache, HTTP/HTTPS |
| **6 – Presentation** | Kryptering (symmetrisk/asymmetrisk), certifikat, encoding (UTF-8) |
| **5 – Session** | Sockets och sessioner (cookies, Session-ID) |
| **4 – Transport** | Portar, TCP (three-way handshake) och UDP |
| **3 – Network** | IP-adresser, gateway, NAT, IP-klasser, TTL, OSPF och RIP |
| **2 – Data Link** | MAC-adress, ARP-tabell, switch, vNIC |
| **1 – Physical** | Kablar (koppar, fiber) och trådlöst |

**Sist i filen** följer jag ett riktigt paket hela vägen:

```
VM  →  Host (gateway)  →  Router  →  Internet  →  Molnets router  →  Molnserver
```

> 💡 **Det viktigaste att komma ihåg:**
> **IP-adressen** är som adressen på ett brev – den är samma hela resan.
> **MAC-adressen** är som vem som bär brevet just nu – den byts vid varje hopp.

---

## 🔐 Del B – Jämförande OS & Behörighetsanalys

**Uppgiften:** Skapa två grupper som får olika åtkomst till en mapp – och att undermapparna **ärver** rättigheterna.

### Upplägget (samma i båda OS)

| Mapp | `g_ledare` (Alice) | `g_personal` (Bob) |
|---|---|---|
| `Projekt/Ledning` | ✅ Läsa/skriva | ❌ Ingen åtkomst |
| `Projekt/Gemensamt` | ✅ Läsa/skriva | ✅ Läsa/skriva |

### Jag gjorde det i 5 steg

1. Skapa användarna **Alice** och **Bob**
2. Skapa grupperna och lägga in användarna
3. Skapa mappen `Projekt` med undermapparna `Ledning` och `Gemensamt`
4. Ge rätt behörigheter (med arv)
5. Testa att allt fungerar

### Windows vs Linux – snabb jämförelse

| Steg | 🖥️ Windows | 🐧 Linux |
|---|---|---|
| Skapa användare | `net user Alice alice /add` | `sudo adduser alice` |
| Skapa grupp | `net localgroup g_ledare /add` | `sudo groupadd g_ledare` |
| Lägg till i grupp | `net localgroup g_ledare Alice /add` | `sudo usermod -aG g_ledare alice` |
| Ge behörighet | `icacls` | `setfacl` |
| Arv till nya filer | `(OI)(CI)` | `-d` (default ACL) |
| Kolla behörighet | `icacls <mapp>` | `getfacl <mapp>` |

---

## 🗂️ Mappstruktur

```
📁 repo
├── 📄 README.md
├── 📄 A_Avancerad-Nätversanalys-&-Trafikflöden(Mål3).md
├── 📄 B_Jämförande-OS-&-behörighetsanalys-(Mål2).md
└── 📁 Bilder/        ← skärmdumpar och bilder som används i filerna
```

---

## 🛠️ Miljö jag använde

- **Windows** i en virtuell maskin (VM)
- **Linux** (Ubuntu) i en virtuell maskin
- **CMD** (kör som administratör) och **Terminal** med `sudo`

---

## ⌨️ Bra kommandon att kunna

| Vad vill du göra? | Windows | Linux |
|---|---|---|
| Fråga DNS om en IP-adress | `nslookup example.com` | `nslookup example.com` |
| Se öppna portar | `netstat -an` | `ss -tunp` |
| Se ARP-tabellen | `arp -a` | `ip neigh` |
| Följa paketets väg | `tracert example.com` | `traceroute example.com` |
| Se routingtabellen | `route print` | `ip route` |

---

**Skriven av:** Bilal Al Ali (README.md är skriven av AI)  
 **Utbildning:** IT Infrastructure Secure Cloud – Chas Academy
