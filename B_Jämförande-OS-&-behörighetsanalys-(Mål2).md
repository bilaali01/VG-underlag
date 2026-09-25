### Uppgiten säger att jag ska jobba i **Windows/Linux** och skapa 2 grupper med olika behörigheter, med tanke på **arv**.

***Jag delade upp det i 5 steg...***  

***steg 1:* Skapa Users**  
- Alice  
- Bob  

***steg 2:* Skapa Grupperna och lägger till Users i Grupperna**  
- g_ledare (Alice) 
- g_personal (Bob)  

***steg 3:* Skapa filen Projekt med två undermappar *(Ledning, Gemensamt)***  
- Projekt  
  - Ledning  
  - Gemensamt  

***steg 4:* Tilldela rätt Åtkomast enligt `arv`**  
- ***Ledning***  
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Ingen åtkomst)  
- ***GemensamBilder/Bilder/image.png
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Läsa/skriva)   

***steg 5:* Testa så att rätt grupp fått rätt åtkomst : JA/NEJ**  

***  
***

### Windows:  

**Förklarar kommando Windows**  
- **icacls :** Verktyg i windows som styr behörighertna på filer och mappar.
- **net :** Verktyg i windows som styr (Nätverk, användare och grupper)  
- **(OI) :** Gäller för alla filer inne i mappen.  
- **(CI) :** Gäller för alla udnermappar inne i mappen.  
- **(M) :** (Modify/ändra)  
- **(F) :** Full controll
***  


***steg 1:* Skapa Users**  Bilder/Bilder/image-2.png
- Alice  
- Bob  

**Computer mangement (run as admin) > Det ska finnas här `Local Users and Groups` men i min VM syns det inte.. spännande.**

*Jag får skriva det i CMD istället...***

**`CMD > run as admin > yes > skriv koden nedan`**
```Bash  
#Formaten.. 
#net user [namn på användare] [lösenord] /add

net user Alice alice /add  #skapar användare Alice som har lösnord alice
net user bob bob/aBilder/Bilder/image-3.pnge bob som har lösenordet bob

```  
**VIKTIGT:** Users är nu skapde/registrerade men dom sysns inte i `C:\Users` efetrsom man måste logga in i dessa konto först för att det ska komma in i `C:\Users`.

![Bob,Alice](Bilder\image50.png)


***






























***steg 2:* Skapa Grupperna och lägger till Users i Grupperna**  
- g_ledare (Alice) 
- g_personal (Bob)  

```BASH  
#`net` använd din verktygBilder/Bilder/image-5.pngär gjort för lokala grupper och lägg till dessa ändringar...

net localgroup g_ledare /add #skapar gruppen g_ledare  

net localgroup g_ledare Alice /add  #lägger till User `Alice` i gruppen g_ledare.  

#----------Bilder/Bilder/image-6.png

net localgroup g_personal /add #Skapar gruppen g_personal
net localgroup g_personal bob /add  #lägger till alice i gruppen g_personal. 
```  

Bilder/Bilder/image-7.png
![skapat grupperna och la till alice och bob](bilder/image-2.png) 

***  








***steg 3:* Skapa filen Projekt med två undermappar *(Ledning, Gemensamt)***  
- Projekt  
  - Ledning  
  - Gemensamt  

**VIKTIGT: Dessafielrna kommer ligga direkt på `C:\` eftersom om jag skapar dessa filer på en User (Public, bob, Alice, Bilal ) då kommer windows blockera andra från att titta i, därför väljer jag att skapa det på `C:\` så att alla har åtkomst till**  

```  
c:\> mkdir projekt  
c:\Projekt> mkdir Gemensamt  
c:\Projekt> mkdir Ledning  
```  

![Skapat filerna](bilder/image-3.png)

***  













***steg 4:* Tilldela rätt Åtkomast enligt `arv`**  
- ***Ledning***  
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Ingen åtkomst)  
- ***Gemensamt***  
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Läsa/skriva)  


**Ställer in behörigheterna för *Gemensamt*..**
```BASH  
icacls c:\Projekt\Gemensamt /grant g_ledare:(OI)(CI)M  
icacls c:\Projekt\Gemensamt /grant g_personal:(OI)(CI)M

#`icacls` Verktyg i windows som styr behörighertna på filer och mappar.
#`grant` Ge/tillåt.  
#`OI` Gäller för alla filer inne i mappen.  
#`CI`  Gäller för alla udnermappar inne i mappen.  
#`M` Modify/ändra

```  

**Ställer in behörigheterna för *Ledning*..**
```BASH  
icacls c:\Projekt\Ledning /grant g_ledare:(OI)(CI)M  
icacls c:\Projekt\Ledning /deny g_personal:(OI)(CI)F 

#`icacls` Verktyg i windows som styr behörighertna på filer och mappar.  
#`grant` Ge/tillåt.
#`deny` Neka.
#`OI` Gäller för alla filer inne i mappen.  
#`CI`  Gäller för alla udnermappar inne i mappen.  
#`M` Modify/ändra.  
#`F` Full Kontroll.
```  

![råkad för fel stavning.](bilder/image-4.png)  

**OPS: Skrev `/grant` iställer för `/deny` för bob ska absolute inte komma in på `/Ledning` filen.**  
**Fixa problemet genom att `/remove` Åtkomsten och sen tilldela om den rätt igen.**  

```BASH  
icacla c:\Projekt\Ledning /remove g_personal  

icacls c:\Projekt\Ledning /deny g_personal:(OI)(CI)F 

#`icacls` Verktyg i windows som styr behörighertna på filer och mappar.  
#`grant` Ge/tillåt.
#`deny` Neka.
#`OI` Gäller för alla filer inne i mappen.  
#`CI`  Gäller för alla udnermappar inne i mappen.  
#`M` Modify/ändra.  
#`F` Full Kontroll.

```  

![Rätta åtkomst för bob](bilder/image-5.png)  
***  

***steg 5:* Testa så att rätt grupp fått rätt åtkomst : JA/NEJ**  

**Bob får inte komma in på `/Ledning` filen. (Ja han har åtkomst till endast `/Gemensamt`)** 
![bob har rätt åtkomst nu](bilder/image-6.png)  


**Alice (JA), har åtkomst till `(/Ledning och /Gemensamt)`**  
![Alice har rätt Åtkomst](bilder/image-7.png)
***  
***  
***  


### Linux  

**Förklarar kommando Linux**  

- **(rwx):**  
  - r = Read : 
  - w = Write : 
  - x = Execute :

- **setfacl :** Verktyg som styr ACL behörighertna på filer och mappar.
- **(-aG) :** -a=användare G=Group (-aG)=lägg till användare `Bilal` i group `Världensbästa`  
- **(-R) :** Gäller för alla mappar och udnermappar inne i mappen som finns just nu.  
- **(-d) :** Gör att alla nya framtida filer och undermappar som skapas i mappen automatiskt ärver samma behörigheter.  
- **(-m) :** Modify ändrar/lägger till behörighet.
***  


***steg 1:* Skapa Users**  
- Alice  
- Bob  

```BASH  
sudo adduser Alice #Skapar User Alice
sudo adduser Bob #Skapar user Bob
```

![alt text](bilder/image100.png)
***  

***steg 2:* Skapa Grupperna och lägger till Users i Grupperna**  
- g_ledare (Alice) 
- g_personal (Bob)  
```BASH  
sudo groupadd g_ledare #Skapar gruppen g_ledare
sudo groupadd g_personal #Skapar gruppen g_personal 

sudo usermod -aG g_ledare Alice #Lägger till Alice i gruppen g_ledare
sudo usermod -aG g_personal Bob #Lägger till Bob i gruppen g_personal

#(-aG) = -a=användare G=Group (-aG)=lägg till användare `Bilal` i group `Världensbästa`. 

groups Alice #visar vilken grup Alice är med
groups Bob #visar vilken grup Bob är med
```

![alt text](bilder/image51.png)
***  

***steg 3:* Skapa filen Projekt med två undermappar *(Ledning, Gemensamt)***  
- Projekt  
  - Ledning  
  - Gemensamt   

**VIKTIGT: Dessafielrna kommer ligga direkt på `/` eftersom om jag skapar dessa filer på en User-filer (Bob, Alice, skola1 ) då kommer systemet blockera andra från att titta i, därför väljer jag att skapa det på `/` så att alla har åtkomst till**  
```BASH  
sudo mkdir -p Projekt/Ledning #Skapade path filer
sudo mkdir -p Projekt/Gemensamt #skapde även Gemensamt filen i Projekt filen

```  

![alt text](bilder/image52.png)  

***  

***steg 4:* Tilldela rätt Åtkomast enligt `arv`**  
- ***Ledning***  
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Ingen åtkomst)  
- ***Gemensamt***  
  - g_ledare (Alice)(Läsa/skriva)  
  - g_personal (bob)(Läsa/skriva)   

```BASH  
sudo setfacl -R -m g:g-ledare:rwx /Projekt/Ledning
sudo setfacl -d -m g:g-ledare:rwx /Projekt/Ledning  

sudo setfacl -R -m g:g-personal:--- /Projekt/Ledning
sudo setfacl -d -m g:g-personal:--- /Projekt/Ledning
#-------
sudo setfacl -R -m g:g-ledare:rwx /Projekt/Genomsamt
sudo setfacl -d -m g:g-ledare:rwx /Projekt/Genomsamt 

sudo setfacl -R -m g:g-personal:rwx /Projekt/Genomsamt
sudo setfacl -d -m g:g-personal:rwx /Projekt/Genomsamt

#(setfacl) = Verktyg som styr ACL behörighertna på filer och mappar.   
#(-R) = Gäller för alla mappar och udnermappar inne i mappen som finns just nu.  
#(-d) = Gör att alla nya framtida filer och undermappar som skapas i mappen automatiskt ärver samma behörigheter.  
#(-m) = Modify ändrar/lägger till behörighet.
```  

![xt](bilder/image53.png) 


***  

***steg 5:* Testa så att rätt grupp fått rätt åtkomst : JA/NEJ**

```BASH  
getfacl /Projekt/Ledning #Visar alla rättigheterna för filen Ledning.

getfacl /Projekt/Gemensamt #Visar alla rättigheterna för filen Gemensamt.

```

![kontoller behörighet](bilder/image60.png)




//Bilal Al Ali