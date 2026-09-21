# Moment A: Avancerad Nätverksanalys &amp; Trafikflöden (Mål 3)  

**Jag ska rita och förklara ett komplett datatrafikflöde från en klient i labbmiljön,
genom lokalt subnät, via gateway/router och DNS-uppslagning, ända fram till en målserver i
molnet/internet.**  
**Resonemanget ska inkludera hur pakethuvuden (MAC, IP, Port) ändras eller
används på respektive skikt i TCP/IP-modellen.**  
***  
###  
**⬇️OSI Model:⬇️**
![7-layers OSI](Bilder/Capas-modelo-OSI.webp)  
**⬇️TCP/IP Model:⬇️**  

![7-layers OSI](Bilder/OSI-model-vs-TCP-IP-model.webp)  





























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
***-`Symmetrisk`=*** Snabb, krypterar stora mängder data. Aktiveras efter att `Authorization` (what data u can access/change)är klar.  
Skyddar `Confidentiality` (gör data oläslig för utomstående).  
`1 Shared Secret-Key` för encryption och decryption, **Algoritmer:(DES,3DES,AES,S-AES,S-DES)**  
  
  **Viktig:** För att dela denna `1 Shared Secret-Key` med mottagaren, Kan vi inte skicka det klartext via internet, Istället får man hjälp av `Asymmetrisk / Diffie-Hellman även Certifikat` för att öppna säker (tunnel), sedan växlar man tillbaka till Symmetrisk. Eller nyckel kommer man överens om med fysiskt träff.  
  
  ***-`Asymmetrisk:`*** Seg Kräver mycket processorkraft (tunga matematiska operationer = stora primtal/elliptiska kurvor), därför används den bara för att öppna tunneln + signera, sedan växlar man till `Symmetrisk` för bulk-data. har `1 Private-Key` + `1 Public-Key`, små mängder data (Key, signaturer, hashar). Aktiveras FÖRST (under handshake), innan `Symmetrisk` tar över. Säkrar inloggningar (certifikat) och delar ut Symmetriska nyckel (`1 Shared Secret-Key`). Sköter `Authentication` (BankID/Certifiering) och `Non-repudiation` (avsändaren kan inte neka). Skyddar `Integrity` (via digitala signaturer). Den är `PKI (Public Key Infrastructure)`.  **Algoritmer:(RSA,ECC,DSA,DH,ECDHE,ElGamal)**

- **Certifikat:**  Vi säger att du köper en ny dator (Windows) när dator ska börja kommunicera med webbläsare, följande händer i bakgrund.  
1: Klienten genererar `1 Private-Key` (stannar på maskinen), `1 Public-Key` packas ihop med identitetsuppgifter till en **CSR - Certificate Signing Requests.**  
2: **`RA (Registration Authority)`** tar emot **(CSR)** och avgör om uppgifterna är sanna.  
3: **RA** frågar **`DC (Domain Controller)`*(AD)*** “finns den maskinen i domänen och stämmer uppgifterna?”  
4: **DC** svarar att maskinen är ett giltigt domänobjekt.  
5: **RA** godkänner **CSR** och skickar den vidare till **`CA (Certificate Authority).`**  
6: **CA** litar på **RA** och godkänner enligt sin policy. **CA** issues och signerar *certifikatet* med sin egen `Private-Key`.  
  7. *Certifikaten* installeras hos klient och kopplas ihop med klient `Private-Key`.  
  
  **`CRL (Certificate Revocation List)`** = Lista med serienummer på (återkallade) certifikater.  
**`OCSP (Online Certificate Status Protocol)`** = real-time certificate status check.

- **Encoding:** sändare och motagare kommer överens om att bestämma en protokol till hur bokstäver ska se ut, annars blir det konstiga tecken på skärmen, en metod är **UTF-8**.  
***  

#### ***OSI-Layer 5:** `Session-Layer.`* |> ***TCP/IP-Layer 4* `Application-Layer.`**  

- **socket:** Tänk dig du kan öppna flera flikar i din webbläsare. Du öppnar youtube i en flik, då kommer socket skriva såhär `(min pc ip:) 192.168.1.50:51234 (<-en öppen port i min dator) | (YT-sida Ip:)93.184.216.34:443 (<- Via 443-HTTPS-port-YTsida)`, sen öppnar du Netflex i ett annat flik så skriver socket `(min pc ip:) 192.168.1.50:51235 (<-en öppen port i min dator)| (NF-sida Ip:)94.174.116.25:443 (<- Via 443-HTTPS-Port-NFsida)`, som du märker jag kan ansluta mig till samma port från utsidan men använder olika portar på min dator. Påsåsätt alla dina flikar går inte in i varandra, allt är organiserar. Youtube tar rummet (porten **51234**) från din dator och Netflix tar rummet (porten **51235**) olika rum olika kontakter så det inte går in i varandra.  

- **Session:** När du öppnar en flik(hemsida) server skapar en session om dig där det sparar `username:bilal,kundvagn:tröja,Inloggad:ja`, server skikcar tillbaka en **`Session-ID (ses-92748229)`** som sparas i din webbläsare(Cookies),Nu varje gång din webläsare skickar en signal via **(socket)** skickas din (Cookie) automatiskt med så att hemsidan du surfar på känner igen dig sen tidigare. därför kan du se att saker du la i din kundvagn fortfarande finns kvar(tröja).  
***

#### ***OSI-Layer 4:** `Transport-Layer.`* |> ***TCP/IP-Layer 3* `Transport-Layer.`**   
- **Port:** Varje dator har exakt `65 535` Portar. Vi har 3 olika Port katalog..  
     **`0 till 1023` (välkända portar)** registerarde på välkända protokoller/tjänster som Port 80 =HTTP, Port:443=HTTPS Port:22=SSH osv..  
    **`1024 till 49151` (Registrerade portar)** används av en specefik app/program på din enhet.  
    **`49152 till 65535` (Dynamiska Portar)** en tillfärligt Port som din dator skapar när du ska surfa på internet för att kunna ta emot datatrafik.  
>  **Du kan se dina egna öppna portar genom `ss -tunp` i *Linux*, och `netstat -an` i *Windows*. I *Windows* kan du även se väldigt bra info genom...**  
**`Task Manager > Performance > Välj ditt nätverks kort > uppe till höger (...) tryck på dom > Resoure Monitor > här har du tabbelerna för Network, TCP, Listning ports.`**  

- **TCP (Transmission Control Protocol):** Fokus på Kvalitet (missar ingen bit) (seg).  
TCP är ett anslutningsorienterat protokoll. Innan data skickas måste en "Three-Way Handshake" ske för att etablera en session.  
  
  **Buffra data:** Normalt kan datorn samla (buffra) lite data innan den skickar vidare det.  
**Flags:** Bitars kontrollinformation (SYN, ACK, FIN, RST, PSH, URG). De styr paketets syfte.  
**MSS (Maximum Segment Size):** Den största mängden data som får plats i ett segment.  
**Window Size:** Hur mycket data som kan skickas innan en bekräftelse (ACK) krävs.  

- **UDP:**  Fokus på snabbhet, bryr sig inte om flera packets tappas på vägen, vädligt osäker eftersom det sker inte nått form av `Hand shake`, utan data skicaks ut utan kontroll. hahah Trots all problem den skapr så gillar jag den fortfarande. Eftersom `spel,telefonsamtal,videosamtal` osv.. anvädner den. Tänk du pratar emd din kompis i ett annat land, det skicaks packet sen om något packet `tappas bort` på vägen och din vän inte hörde vad du sa, då kan din vän bara be dig att upprepa det du sa. 

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


En Viktigt sak du kan göra det är att se vilka portar du har öppna, vilka TCP connections du har och dina Nätverk aktivetieter, du kan ekelt göra det genom,  
**`Task Manager > Performance > Välj ditt nätverks kort > uppe till höger (...) tryck på dom > Resoure Monitor > här har du tabbelerna för Network, TCP, Listning ports.`**

Vi säger att jag har skapat en VM i min dator och jag går in på internet och skriver www.google.com  

Vad händer med mitt packet( hur öppnas Google.com hos mig)?  

Först tänker jag förkal lite begrepp som DNS,ARP,Ip-address,Port,MAC, Host.


- **Host:** Är min main Pc som VM bor i, den agerar som switch

1. Vm på min dator har en ip Address,MAC,DNS

- **Kryptering:** Ändrar läsbar text `(Plaintext)` **till** oläsbar text `(Chiffertext)` genom avancerade metoder/ekvationer (`Symmetrisk och Asymmetrisk`).  
**-`Symmetrisk`=** Snabb, krypterar stora mängder data. Aktiveras efter att `Authorization` (what data u can access/change)är klar.  
Skyddar `Confidentiality` (gör data oläslig för utomstående).  
`1 Shared Secret-Key` för encryption och decryption, **Algoritmer:(DES,3DES,AES,S-AES,S-DES)**  
**Viktig:** För att dela denna `1 Shared Secret-Key` med mottagaren, Kan vi inte skicka det klartext via internet, Istället får man hjälp av `Asymmetrisk / Diffie-Hellman även Certifikat` för att öppna säker (tunnel), sedan växlar man tillbaka till Symmetrisk. Eller nyckel kommer man överens om med fysiskt träff. 
