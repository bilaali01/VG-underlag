# Moment A: Avancerad Nätverksanalys &amp; Trafikflöden (Mål 3)  

**Jag ska rita och förklara ett komplett datatrafikflöde från en klient i labbmiljön,
genom lokalt subnät, via gateway/router och DNS-uppslagning, ända fram till en målserver i
molnet/internet.**  
**Resonemanget ska inkludera hur pakethuvuden (MAC, IP, Port) ändras eller
används på respektive skikt i TCP/IP-modellen.**  
***  
###  
**⬇️OSI Model:⬇️**
![7-layers OSI](Bilder\Capas-modelo-OSI.webp)  
**⬇️TCP/IP Model:⬇️**  

![7-layers OSI](Bilder\OSI-model-vs-TCP-IP-model.webp)  





























***  
### *VIKTIGT:* Jag kommer gå genom varje OSI-Layer 7-1 (uppifrån neråt).  
***---Senario***: **Klient** `192.168.1.50/24` surfar till `https://example.com` via **Gateway:**`192.168.1.1` 

#### ***OSI-Layer 7:** `Application-Layer.`* |> ***TCP/IP-Layer 4* `Application-Layer.`** 
- **DNS:** Eftersom dator inte förstår våra bokstäver, utan bara siffror. Har vi en `DNS-server` (tabell) lagrar **`webbsida = Ip-address`**.  
När du besöker `example.com` i din webbläsare, då frågar din dator DNS-server **vad betyder `example.com`**, DNS-server letar inne i sin tabell och svarar att `example.com = 94.184.216.35`.  
Du ser på din skärm `example.com` men i bakrunden din dator ser `94.184.216.35`  

- **Cache:** Nu när dator fick veta att `example.com = 94.184.216.35` så sparar din dator det i `webbläsare-minne(Cache)`, istället för att fråga `DNS-server` om samma address varje gång.  

- **HTTP/HTTPS:** Det är protokoller man använder för att berätta för sidan vad man vill ha.  
-`HTTP bor på port 80` Används sällan, eftersom det kommnkationen mellan dig och webbsida är i plaintext (vem som helst kan läsa) osäker protokoll.  
-`HTTPS bor på port 443` Används idag. Mycket säker eftersom kommnucation mellan dig och websidan är `krypterad`, bara du och webbsidan kan förstå varandra.  

**> Du kan fråga din DNS-server om vad en sida har för Ip-address genom `nslookup example.com`.**  
***
#### ***Layer 6:** `Presentation-Layer.`* | ***TCP/IP-Layer 4* `Application-Layer.`**  

- **Kryptering:** Ändrar läsbar text `(Plaintext)` **till** oläsbar text `(Chiffertext)` genom avancerade metoder/ekvationer (`Symmetrisk och Asymmetrisk`).  
**-`Symmetrisk`=** Snabb, krypterar stora mängder data. Aktiveras efter att `Authorization` (what data u can access/change)är klar.  
Skyddar `Confidentiality` (gör data oläslig för utomstående).  
`1 Shared Secret-Key` för encryption och decryption, **Algoritmer:(DES,3DES,AES,S-AES,S-DES)**  
**Viktig:** För att dela denna `1 Shared Secret-Key` med mottagaren, Kan vi inte skicka det klartext via internet, Istället får man hjälp av `Asymmetrisk / Diffie-Hellman även Certifikat` för att öppna säker (tunnel), sedan växlar man tillbaka till Symmetrisk. Eller nyckel kommer man överens om med fysiskt träff. 
  
**-`Asymmetrisk`:** har `1 Private-Key` + `1 Public-Key`  

- **Certifikat:**  

- **Encoding:**



#### ***OSI-Layer 5:** `Session-Layer.`* |> ***TCP/IP-Layer 4* `Application-Layer.`**   



#### ***OSI-Layer 4:** `Transport-Layer.`* |> ***TCP/IP-Layer 3* `Transport-Layer.`**   
- **Port:**  
- **TCP:**  
- **UDP:**

#### ***OSI-Layer 3:** `Network-Layer.`* |> ***TCP/IP-Layer 2* `Network-Layer.`**  

- **Ip-address:**  
- **Gateway:**  
- **RIP:**  
- **OSPF**



#### ***OSI-Layer 2:** `Data_Link-Layer.`* |> ***TCP/IP-Layer 1* `Link-Layer.`**  
- **ARP-tabllen:** En tabell i Router. Sparar `intern data` som `Private-Ip + MAC` att `Ip 192.168.1.2` bor på enheten med `MAC: 00:1A:8B:3c:4D:5E`.  
- **MAC-address:**  
- **Switch:**  
- **vNIC:**


#### ***OSI-Layer 1:** `Physical-Layer.`* |> ***TCP/IP-Layer 1* `Link-Layer.`** 
- **Kablarna:**


***
***  
***

- **Ip-Address:** Varje enhet har en Private-Ip, den sparas i ARP-tabllen, tilldelas via din Router (DHCP) eller väljer du själv. 

- Du kan se att dina `Viretuell Maskin(VM)` har ett annat **default gateway** än vad din `Host` har, detta eftersom dessa `Viretuell Maskin(VM)` bor på din `Host` så det använder en **osylig `(vNIC)` inuti din `Host`** och då är det den som heter `Default Switch` **(`(Vnic)nätverkskortet` för `Viretuell Maskin(VM)`)** Med andra ord din `Host` är `Switch + router + DHCP-server + DNS-forwarder` för dina `Viretuell Maskin(VM)`.  
- Om du vill ut på internet via din `Viretuell Maskin(VM)` skickas packet till din `Viretuell Maskin(VM)` `Gateway` som är din `Host`.  
`Host` Skickar vidare till sin `gateway` (Router).  
(Router) skickar vidare packet via `(RIP eller OSPF)` alltså packet mellanlandar hos flera Routerar innan den når destenation. 

- 

***  


