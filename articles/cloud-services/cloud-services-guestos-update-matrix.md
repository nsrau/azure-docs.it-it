<properties 
   pageTitle="Altre informazioni sui rilasci più recenti del sistema operativo guest Azure | Azure" 
   description="Notizie sui rilasci più recenti e sulla compatibilità SDK per il sistema operativo guest di Servizi cloud di Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="04/17/2015"
   ms.author="adegeo"/>

# Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK
Fornisce informazioni aggiornate sui rilasci del sistema operativo guest Azure più recente per i servizi cloud. Queste informazioni sono utili per pianificare il percorso di aggiornamento prima che un sistema operativo guest venga disabilitato.

> [AZURE.IMPORTANT]Questa pagina è applicabile ai ruoli Web e ai ruoli di lavoro dei servizi cloud in esecuzione su un sistema operativo guest. Non si applica alle macchine virtuali IaaS. Se si configurano i ruoli per l'uso degli aggiornamenti automatici del sistema operativo guest come descritto in [Impostazioni di aggiornamento del sistema operativo guest Azure][], non è indispensabile leggere questa pagina.

<!-- -->
<br />

> [AZURE.TIP]Sottoscrivere il [feed RSS di aggiornamento del sistema operativo guest][rss] per ricevere notifiche più tempestive su tutte le modifiche al sistema operativo guest. Le modifiche indicate nel feed verranno integrate in questa pagina all'incirca ogni settimana.


## Novità e aggiornamenti

###### **17 aprile 2015**
Le versioni del sistema operativo guest 4.19, 3.26, 2.38 sono state rilasciate in data odierna.

Questo rilascio contiene [MS15-034](https://technet.microsoft.com/library/security/MS15-034), una patch **critica** per i server HTTP Windows.

Le versioni del sistema operativo guest 4.17, 3.24, 2.36 verranno disabilitate il 17 maggio 2015.

###### **6 aprile 2015**
Le versioni del sistema operativo guest 4.18, 3.25, 2.37 sono state rilasciate il 2 aprile 2015.

Le versioni del sistema operativo guest 4.16, 3.23, 2.35 verranno disabilitate il 2 maggio 2015.


###### **11 marzo 2015**
Le versioni del sistema operativo guest 4.17, 3.24 e 2.36 sono state rilasciate il 9 marzo 2015.

Per le versioni del sistema operativo guest 4.15, 3.22 2.34 è stata definita come data di disabilitazione il 9 marzo 2015.


###### **29 gennaio 2015**
La data di disabilitazione delle versioni del sistema operativo guest OS 4.14, 4.13, 3.21, 3.20, 2.33, 2.32 (rilasciate a novembre) è stata rinviata. La matrice dei rilasci del sistema operativo guest indicata di seguito è stata aggiornata.


###### **13 gennaio 2015, aggiornato il 15 gennaio 2015**
La versione di dicembre del sistema operativo guest è stata rilasciata il 14 gennaio 2015.


###### **13 gennaio 2015**
La distribuzione di gennaio del sistema operativo guest è prevista per il 19 gennaio 2015 o data successiva con aggiornamento automatico dei servizi cloud. La distribuzione di gennaio del sistema operativo guest verrà rilasciata nel mese di febbraio. SSL 3.0 verrà disattivato per impostazione predefinita, come indicato nell'aggiornamento della sicurezza di gennaio. Questo articolo verrà aggiornato quando saranno disponibili altre informazioni.

Come [annunciato in precedenza][ssl3 announcement], l'aggiornamento della sicurezza di gennaio al sistema operativo guest PaaS disabiliterà il supporto SSL 3.0 per impostazione predefinita, come consigliato dall'[avviso di sicurezza 3009008 Microsoft][]. Questa modifica si aggiunge a tutti gli altri aggiornamenti della sicurezza mensili. I clienti PaaS, per i quali sono abilitati gli aggiornamenti automatici, possono verificare l'eventuale impatto sulla compatibilità delle applicazioni quando SSL 3.0 è disattivato tramite l'esecuzione di questo [script di correzione][ssl3-fixit] che disabiliterà in modo proattivo il supporto SSL 3.0.

###### **16 dicembre 2014. Aggiornato il 7 gennaio 2015**
Il rilascio di dicembre del sistema operativo guest è previsto per il 9 gennaio 2015 o data successiva.


###### **11 novembre 2014**

La versione di novembre (4.14, 3.21 e 2.33) è stata distribuita l'11 novembre. Questo aggiornamento è stato distribuito tramite push in anticipo perché include l'aggiornamento [MSRC Bollettino Microsoft sulla sicurezza MS14-066 - Critico][MS14-066]. È consigliabile riavviare nei prossimi giorni i ruoli Web e di lavoro inclusi nell'aggiornamento automatico per ricevere la correzione.

###### **10 novembre 2014**
La data di disabilitazione della versione di ottobre (4.13, 3.20 e 2.32) è stata aggiornata in base ai commenti e suggerimenti dei clienti. La versione verrà sempre disabilitata una volta trascorsi almeno due mesi dalla data di rilascio.

###### **4 novembre 2014**
La versione di ottobre (4.13, 3.20 e 2.32) è stata distribuita il 4 novembre 2014. Include la patch MSRC che causava problemi nelle versioni di agosto e settembre. Per risolvere questo problema, la versione di ottobre include .NET 3.5 e 3.5.1 che sono preinstallati, ma non abilitati. Gli script che tentano di installare .NET 3.5 o 3.5.1 la abiliteranno e restituiranno un messaggio di operazione completata per l'installazione di .NET; inoltre, eviteranno il riprodursi del problema di installazione completa causato dalla patch MSRC.



## Informazioni sui rilasci del sistema operativo guest

In questa sezione sono elencate le versioni del sistema operativo guest attualmente supportate. Per le famiglie e le versioni del sistema operativo guest sono previste una data di rilascio, una data di disabilitazione e una data di scadenza. A partire dalla data di rilascio, è possibile selezionare manualmente una versione di sistema operativo guest nel portale di gestione. Un sistema operativo guest viene rimosso dal portale di gestione lo stesso giorno o dopo la relativa data di "disabilitazione". È quindi "in transizione", ma è supportato con capacità limitate di aggiornamento di una distribuzione. La data di scadenza è la data in cui è prevista la rimozione completa di una versione o di una famiglia dal sistema Azure. Alla data di scadenza i servizi cloud ancora in esecuzione in una versione verranno arrestati, eliminati o aggiornati in modo forzato a una versione più recente, come descritto in [Criteri relativi al supporto e al ritiro del sistema operativo guest Azure][retirepolicy].

Microsoft supporta almeno le due versioni più recenti di ogni famiglia del sistema operativo guest supportata. La data di disabilitazione di una versione di sistema operativo guest esistente può essere posticipata per garantire che almeno due versioni rilasciate rimangano sempre disponibili per la distribuzione.

> [AZURE.WARNING]Il ritiro della famiglia di sistemi operativi guest 1 è iniziato il 1° giugno 2013 e il completamento è previsto a breve. Non creare nuove installazioni e aggiornare quelle precedenti mediante questa famiglia di sistemi operativi guest. Per altre informazioni, vedere [Informazioni sul ritiro della famiglia di sistemi operativi guest Azure 1][fam1retire]

Il sistema operativo guest include una configurazione diversa da quella predefinita di Windows Server. Per altre informazioni, vedere [Differenze tra il sistema operativo guest Azure e Windows Server predefinito][server and gos].

### Famiglia, versione e rilascio del sistema operativo guest
Le famiglie dei sistemi operativi guest sono basate sulle versioni rilasciate di Microsoft Windows Server e il sistema operativo guest è il sistema operativo sottostante in cui viene eseguito Servizi cloud di Azure. A ogni sistema operativo guest sono associati una famiglia, una versione e un numero di rilascio.

Una **famiglia di sistemi operativi guest** corrisponde a un rilascio del sistema operativo Windows Server su cui è basato un sistema operativo guest. Ad esempio, la famiglia 3 è basata su Windows Server 2012.

Con l'espressione **versione del sistema operativo guest** si intendono l'immagine della famiglia di sistemi operativi e le patch rilevanti di [Microsoft Security Response Center (MSRC)][msrc] disponibili al momento dell'introduzione della nuova versione del sistema operativo guest. È possibile che non siano incluse tutte le patch. I numeri iniziano da 0 e vengono incrementati di 1 a ogni aggiunta di un nuovo set di aggiornamenti. Gli zeri finali vengono visualizzati solo se sono importanti; ad esempio, la versione 2.10 è una versione diversa e successiva rispetto alla versione 2.1.

Con il termine **rilascio del sistema operativo guest** si intende un nuovo rilascio di una versione del sistema operativo guest. Il nuovo rilascio viene introdotto se durante la fase di test Microsoft rileva problemi che richiedono l'esecuzione di modifiche. L'ultimo rilascio sostituisce sempre quelli precedenti, che siano pubblici o meno. Il portale di gestione consente agli utenti di scaricare soltanto l'ultimo rilascio disponibile per una determinata versione. Le distribuzioni in cui si usa una versione precedente in genere non vengono forzate all'aggiornamento in base alla gravità del bug.

Nell'esempio seguente, 2 rappresenta la famiglia, 12 la versione e "rel2" il rilascio.

**Rilascio del sistema operativo guest** - 2.12 rel2

**Stringa di configurazione per questo rilascio** - WA-GUEST-OS-2.12_201208-02

Nella stringa di configurazione per un sistema operativo guest sono contenute queste stesse informazioni, insieme a una data che indica quali patch di MSRC sono state prese in considerazione per il rilascio. In questo esempio, sono state incluse le patch di MSRC prodotte per Windows Server 2008 R2 fino ad agosto 2012. Vengono incluse solo le patch che si applicano specificatamente alla versione di Windows Server. Ad esempio, se una patch MSRC si applica a Microsoft Office, non verrà inclusa poiché il prodotto non fa parte dell'immagine di base di Windows Server.

## Rilasci

## Rilasci della famiglia 4
**Windows Server 2012 R2**

Supporta .NET 4.0, 4.5, 4.5.1, 4.5.2 (Nota 2)

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 4.19 | WA-GUEST-OS-4.19_201504-01 | 17 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 4.21 | Da definire |
| 4.18 | WA-GUEST-OS-4.18_201503-01 | 2 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 4.20 | Da definire |
| 4.17 | WA-GUEST-OS-4.17_201502-01 | 9 marzo 2015 | 17 maggio 2015 | Da definire |
| 4.16 | WA-GUEST-OS-4.16_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | Da definire |
| 4.15 | WA-GUEST-OS-4.15_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | Da definire |
| 4.14 | WA-GUEST-OS-4.14_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | Da definire |
| 4.13 | WA-GUEST-OS-4.13_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | Da definire |
| 4.12 (Nota 1) | WA-GUEST-OS-4.12_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 4.11 (Nota 1) | WA-GUEST-OS-4.11_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 4.10 | WA-GUEST-OS-4.10_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 4.9 | WA-GUEST-OS-4.9_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 4.8 | WA-GUEST-OS-4.8_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |

## Versioni della famiglia 3

**Windows Server 2012**

Supporta .NET 4.0, 4.5

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 3.26 | WA-GUEST-OS-3.26_201504-01 | 17 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 3.28 | Da definire |
| 3.25 | WA-GUEST-OS-3.25_201503-01 | 2 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 3.27 | Da definire |
| 3.24 | WA-GUEST-OS-3.24_201502-01 | 9 marzo 2015 | 17 maggio 2015 | Da definire |
| 3.23 | WA-GUEST-OS-3.23_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | Da definire |
| 3.22 | WA-GUEST-OS-3.22_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | Da definire |
| 3.21 | WA-GUEST-OS-3.21_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | Da definire |
| 3.20 | WA-GUEST-OS-3.20_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | Da definire |
| 3.19 (Nota 1) | WA-GUEST-OS-3.19_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 3.18 (Nota 1) | WA-GUEST-OS-3.18_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 3.17 | WA-GUEST-OS-3.17_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 3.16 | WA-GUEST-OS-3.16_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 3.15 | WA-GUEST-OS-3.15_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |


## Versioni della famiglia 2

**Windows Server 2008 R2 SP1**

Supporta .NET 3.5, 4.0

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 2.38 | WA-GUEST-OS-2.38_201504-01 | 17 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 2.40 | Da definire |
| 2.37 | WA-GUEST-OS-2.37_201503-01 | 2 aprile 2015 | Verrà aggiornata al momento del rilascio della versione 2.39 | Da definire |
| 2.36 | WA-GUEST-OS-2.36_201502-01 | 9 marzo 2015 | 17 maggio 2015 | Da definire |
| 2.35 | WA-GUEST-OS-2.35_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | Da definire |
| 2.34 | WA-GUEST-OS-2.34_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | Da definire |
| 2.33 | WA-GUEST-OS-2.33_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | Da definire |
| 2.32 | WA-GUEST-OS-2.32_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | Da definire |
| 2.31 (Nota 1) | WA-GUEST-OS-2.31_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 2.30 (Nota 1) | WA-GUEST-OS-2.30_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 2.29 | WA-GUEST-OS-2.29_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 2.28 | WA-GUEST-OS-2.28_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 2.27 | WA-GUEST-OS-2.27_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |




### Versioni della famiglia 1
La **FAMIGLIA 1** è stata [ritirata][fam1retire].

#### Nota 1
Le versioni di agosto e settembre 2014 sono state distribuite parzialmente a causa di problemi relativi a una [patch MSRC][MS14-046]. A causa di questo problema non è possibile installare manualmente .NET 3.5.1 nelle famiglie di sistemi operativi guest 3 e 4. La versione è stata disabilitata a titolo precauzionale affinché i clienti non potessero selezionarla manualmente.

#### Nota 2
Alla data del 19 settembre 2014, .NET 4.5.2 non è stato specificamente testato nel sistema operativo guest Azure, ma il sistema operativo guest è essenzialmente equivalente a Windows Server. Le stesse regole di compatibilità che si applicano al prodotto Windows Server si applicano quindi anche alle famiglie di sistemi operativi guest equivalenti. Se si verifica un'eccezione a questi criteri, contattare il [supporto di Azure][azuresupport]. Microsoft si impegnerà a risolvere il problema. [Pacchetto di installazione manuale per .NET 4.5.2][net install pkg].

### Aggiornamenti di MSRC inclusi nel sistema operativo guest
L'elenco di patch incluse con il rilascio del sistema operativo di ogni mese è disponibile [qui][patches].

## Supporto SDK

In questa tabella viene indicata la famiglia di sistemi operativi guest compatibile con le diverse versioni di Azure SDK. Per altre informazioni non contenute in questa tabella, vedere [Informazioni su supporto e ritiro di Azure SDK per .NET e API][retire policy sdk]. Le informazioni contenute in questo elenco sostituiscono quelle seguenti.

> [AZURE.IMPORTANT]Per garantire il corretto funzionamento del servizio, è necessario distribuirlo nella versione del sistema operativo guest compatibile con quella di Azure SDK usata per sviluppare il servizio. In caso contrario, nel cloud del servizio distribuito potrebbero verificarsi degli errori che non erano evidenti nell'ambiente di sviluppo.

| Famiglia del sistema operativo guest | Versioni SDK supportate |
| --------------- | ---------------------- |
| 4 | Versione 2.1 e successive |
| 3 | Versione 1.8 e successive |
| 2 | Versione 1.3 e successive |
| 1 | Versione 1.0 e successive |


## Processo di aggiornamento del sistema operativo guest
In questa pagina vengono fornite informazioni sui prossimi rilasci del sistema operativo guest. I clienti hanno indicato di voler sapere quando viene rilasciata una versione poiché i ruoli del servizio cloud vengono riavviati se impostati per l'aggiornamento automatico. Le versioni del sistema operativo guest vengono in genere rilasciate almeno 5 giorni dopo il rilascio dell'aggiornamento di MSRC che avviene il secondo martedì di ogni mese. Le nuove versioni includono tutte le patch importanti di MSRC per ogni famiglia del sistema operativo guest.

Gli aggiornamenti di Microsoft Azure vengono rilasciati costantemente. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da una serie di fattori che sono troppo numerosi per essere elencati in questa pagina. Inoltre, Azure viene eseguito su centinaia di migliaia di computer. Ciò significa che è impossibile specificare una data e un'ora esatte del riavvio dei ruoli. Il [feed RSS di aggiornamento del sistema operativo guest][rss] verrà aggiornato con le informazioni più recenti disponibili, ma tenere presente che i tempi sono approssimativi. Siamo consapevoli del fatto che questo rappresenta un problema per i clienti e stiamo preparando un piano per limitare o impostare una tempistica per i riavvii.

Quando viene pubblicato un nuovo rilascio del sistema operativo guest, la propagazione completa in Azure può richiedere tempo. Man mano che i servizi vengono aggiornati al nuovo sistema operativo guest, vengono riavviati rispettando i domini di aggiornamento. Un rilascio sarà innanzitutto fornito ai servizi per cui sono stati impostati gli aggiornamenti automatici. Dopo l'aggiornamento, la nuova versione del sistema operativo guest sarà elencata per il servizio nel portale di gestione di Azure. Durante questo periodo possono essere introdotti dei nuovi rilasci. Alcune versioni potrebbero essere distribuite per periodi più lunghi ed è possibile che i riavvii di aggiornamenti automatici non si verifichino per molte settimane dopo la data di rilascio ufficiale. Quando un sistema operativo guest diventa disponibile, è possibile scegliere esplicitamente tale versione dal portale o nel file di configurazione. Per altre informazioni, vedere [Aggiornare il sistema operativo guest Azure dal portale di gestione][update guest os portal] e [Aggiornare il sistema operativo guest Azure modificando il file di configurazione del servizio][update guest os svc].

Per moltissime informazioni importanti sui riavvii e sui puntatori ad altre informazioni sui dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post del blog su MSDN relativo [ai riavvii dell'istanza del ruolo dovuti ad aggiornamenti del sistema operativo][restarts].

Se si aggiorna manualmente il sistema operativo guest, leggere le informazioni importanti seguenti sul [supporto e sul ritiro del sistema operativo guest][retirepolicy].


## Criteri relativi al supporto e al ritiro del sistema operativo guest
I criteri relativi al supporto e al ritiro del sistema operativo guest sono descritti [qui][retirepolicy].
 
## Archivio notizie

**20 ottobre 2014. Aggiornata il 4 novembre 2014.** La versione di settembre (4.12, 3.19, 2.31 e 1.39) è stata distribuita parzialmente a causa di problemi relativi alla stessa [patch MSRC MS14-046][MS14-046] che genera errori durante il tentativo di installare .NET 3.5 o 3.5.1 nella famiglia di sistemi operativi guest 3 o 4. .NET 3.5.x NON è ufficialmente supportato in entrambe le famiglie, ma Microsoft sta tentando di risolvere il problema relativo a questa modifica del comportamento, perché influisce su alcune installazioni dei clienti e non era stata annunciata. Le date di disabilitazione dei sistemi operativi guest precedenti (giugno e luglio) verranno posticipate in modo da rendere disponibili almeno due versioni complete di sistemi operativi guest e garantirne il supporto. Una soluzione al problema di installazione di .NET è presente nella versione di ottobre 2014.

Nella versione di ottobre sono inclusi .NET 3.5 e 3.5.1 preinstallati ma disabilitati, oltre alla patch MSRC indicata in precedenza. Gli script che tentano di installare .NET 3.5 o 3.5.1 la abiliteranno e restituiranno un messaggio di operazione completata per l'installazione di .NET; inoltre, eviteranno il riprodursi del problema di installazione causato dalla patch MSRC.

A seguito dell'implementazione parziale delle ultime due versioni, gli utenti che hanno abilitato l'aggiornamento automatico o che hanno implementato nuove installazioni potrebbero avere installato una di queste versioni del sistema operativo guest. Nella tabella seguente sono riportate le versioni del sistema operativo guest che consentono l'installazione di .NET 3.5 o 3.5.1 nelle famiglie 3 e 4. Attualmente, se una versione consente l'installazione significa che NON è installata la patch MSRC MS14-046.

| Versione sistema operativo | Può installare .NET 3.5 | Include la patch MSRC [MS14-046][] |
| --- | --- | --- |
| Tutte le versioni successive del sistema operativo guest | Sì | Sì |
| WA-GUEST-OS-4.13_201410-01 | Sì | Sì |
| WA-GUEST-OS-4.12_201409-02 | No | Sì |
| WA-GUEST-OS-4.12_201409-01 | No | Sì |
| WA-GUEST-OS-4.11_201408-02 | Sì | No |
| WA-GUEST-OS-4.11_201408-01 | No | Sì |
| WA-GUEST-OS-4.10_201407-01 | Sì | No |

**20 ottobre 2014**. Poiché le versioni di agosto e settembre sono state distribuite solo parzialmente, è importante tenere presente quanto segue:

1. Le modifiche apportate alla crittografia descritte in Differenze tra il sistema operativo guest Azure e Windows Server predefinito non sono state distribuite all'intero ambiente Azure. I clienti non inclusi nelle versioni di agosto o settembre riceveranno queste modifiche nella versione di ottobre. 

2. I sistemi operativi guest di agosto e settembre sono stati disabilitati nel portale di gestione e non è possibile sceglierli manualmente. La disabilitazione ha lo scopo di evitare problemi che possono verificarsi se si seleziona questa versione del sistema operativo guest.

3. Le date di disabilitazione di alcune versioni precedenti sono state posticipate per garantire la continuità della disponibilità nel portale e il supporto per almeno due versioni di sistemi operativi guest in ogni famiglia.

## Archivio delle versioni

### Famiglia 4

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 4.7 | WA-GUEST-OS-4.7_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 4.6 | WA-GUEST-OS-4.6_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 4.5 | WA-GUEST-OS-4.5_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 4.4 | WA-GUEST-OS-4.4_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 4.3. | WA-GUEST-OS-4.3_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 4.2 | WA-GUEST-OS-4.2_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 4.1 | WA-GUEST-OS-4.1_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 4.0 rel3 | WA-GUEST-OS-4.0_201309-03 | 9 ottobre 2013 Resa pubblica il 18 ottobre. | N/D | 14 maggio 2014 |
 


### Famiglia 3

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 3.14 | WA-GUEST-OS-3.14_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 3.13 | WA-GUEST-OS-3.13_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 3.12 | WA-GUEST-OS-3.12_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 3.11 | WA-GUEST-OS-3.11_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 3.10 | WA-GUEST-OS-3.10_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 3.9 | WA-GUEST-OS-3.9_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 3.8 | WA-GUEST-OS-3.8_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 3.7 rel3 | WA-GUEST-OS-3.7_201309-03 | 9 ottobre 2013 | N/D | 14 maggio 2014 |
| 3.7 rel1 | WA-GUEST-OS-3.7_201309-01 | 23 settembre 2013 | N/D | 14 maggio 2014 |

### Famiglia 2

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 2.26 | WA-GUEST-OS-2.26_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 2.25 | WA-GUEST-OS-2.25_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 2.24 | WA-GUEST-OS-2.24_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 2.23 | WA-GUEST-OS-2.23_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 2.22 | WA-GUEST-OS-2.22_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 2.21 | WA-GUEST-OS-2.21_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 2.20 | WA-GUEST-OS-2.20_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 2.19 rel3 | WA-GUEST-OS-2.19_201309-03 | 9 ottobre 2013 | N/D | 14 maggio 2014 |
| 2.19 rel1 | WA-GUEST-OS-2.19_201309-01 | 23 settembre 2013 | N/D | 14 maggio 2014 |
[Impostazioni di aggiornamento del sistema operativo guest Azure]: https://msdn.microsoft.com/library/azure/ff729420.aspx
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[avviso di sicurezza 3009008 Microsoft]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md

<!--HONumber=54--> 