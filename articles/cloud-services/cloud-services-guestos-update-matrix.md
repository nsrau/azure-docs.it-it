<properties 
   pageTitle="Altre informazioni sui rilasci più recenti del sistema operativo guest Azure | Microsoft Azure" 
   description="Notizie sui rilasci più recenti e sulla compatibilità SDK per il sistema operativo guest di Servizi cloud di Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="02/22/2016"
   ms.author="yuemlu"/>

# Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK
Fornisce informazioni aggiornate sui rilasci del sistema operativo guest Azure più recente per i servizi cloud. Queste informazioni sono utili per pianificare il percorso di aggiornamento prima che un sistema operativo guest venga disabilitato.

> [AZURE.IMPORTANT] Questa pagina è applicabile ai ruoli Web e ai ruoli di lavoro dei servizi cloud in esecuzione su un sistema operativo guest. Non si applica alle macchine virtuali IaaS. Se si configurano i ruoli per l'uso degli aggiornamenti automatici del sistema operativo guest come descritto in [Impostazioni di aggiornamento del sistema operativo guest Azure][], non è indispensabile leggere questa pagina.

<!-- -->
<br />

> [AZURE.TIP] Sottoscrivere il [feed RSS di aggiornamento del sistema operativo guest][rss] per ricevere notifiche più tempestive su tutte le modifiche al sistema operativo guest. Le modifiche indicate nel feed verranno integrate in questa pagina all'incirca ogni settimana.



## Novità e aggiornamenti

###### **22 febbraio 2016**
L'implementazione del sistema operativo guest di febbraio è iniziata il 22 febbraio 2016 e dovrebbe essere rilasciata il 9 marzo 2016.

###### **18 gennaio 2016**
L'implementazione del sistema operativo guest di gennaio è iniziata il 18 gennaio 2016 e dovrebbe essere rilasciata il 12 febbraio 2016.

###### **4 gennaio 2016**
Il sistema operativo guest del 2 novembre 2015 è stato rilasciato per la distribuzione il 4 gennaio 2016. Questa versione del sistema operativo non è impostata come sistema operativo predefinito per l'aggiornamento automatico, quindi i tempi di provisioning della distribuzione del sistema operativo guest alla versione del 2 novembre 2015 sono leggermente più lunghi.

###### **18 dicembre 2015**
La data di rilascio del sistema operativo guest 201511-02 novembre è stata posticipata dal 16 dicembre 2015 al 4 gennaio 2016.

###### **9 dicembre 2015**
L’implementazione del sistema operativo guest di dicembre è iniziata il 10 dicembre 2015 e dovrebbe essere rilasciata l’8 gennaio 2016.

###### **12 novembre 2015**  
Il 7 agosto 2014 Microsoft ha annunciato che il supporto per .NET Framework 4, 4.5 e 4.5.1 terminerà il 12 gennaio 2016. Per i clienti e gli sviluppatori è consigliabile completare l'aggiornamento locale a .NET Framework 4.5.2 12 entro il 12 gennaio 2016 per continuare a ricevere supporto tecnico e aggiornamenti della sicurezza. Per maggiori dettagli, visitare il sito dedicato ai criteri relativi al ciclo di vita del supporto per Microsoft .NET Framework.

Il 27 ottobre abbiamo annunciato che Azure aggiornerà .NET Framework all'interno della famiglia di sistemi operativi guest Azure (sistema operativo guest) 2. x, 3.x e 4. x a .NET Framework 4.5.2 nella prossima versione del sistema operativo guest di novembre. Da allora, commenti e suggerimenti dei clienti ci hanno consigliato di rimandare l'aggiornamento automatico a una versione del sistema operativo con .NET 4.5.2 e di fornire un'immagine con .NET 4.5.2 per il test di convalida.

Per meglio soddisfare le esigenze dei clienti e garantire un agevole aggiornamento a .NET 4.5.2, Azure aggiornerà .NET Framework nella famiglia di sistemi operativi guest Azure (sistema operativo guest) 2. x, 3.x e 4. x a .NET Framework 4.5.2 nella versione di gennaio 2016 del sistema operativo guest. I servizi cloud che eseguono la famiglia di sistemi operativi guest 2. x, 3.x e 4. x con gli aggiornamenti automatici abilitati verranno aggiornati alla versione del sistema operativo guest di gennaio 2016 con .NET Framework 4.5.2. Nel mese di novembre, la versione di .NET Framework installata nel sistema operativo predefinito non verrà modificata. Per consentire ai clienti di convalidare il servizio cloud con .NET 4.5.2, Azure offre un secondo set di versioni del sistema operativo 201511-02 di novembre con .NET 4.5.2 per la distribuzione manuale.

Nella tabella seguente sono schematizzate le modifiche che verranno applicate.
 
| Famiglia del sistema operativo guest | .NET Framework installato prima della versione 201511-02 del sistema operativo guest | .NET Framework installato con la versione 201511-02 del sistema operativo guest | .NET Framework installato con la versione 201512-01 del sistema operativo guest | .NET Framework installato con la versione 201601-01 o successiva del sistema operativo guest |
| --------------- | ---------------- | ---------------- | ---------------- | ---------------- |
| Famiglia di sistemi operativi 2.x basata su Windows Server 2008 R2 | .NET 3.5, .NET 4.0 | .NET 3.5, .NET 4.5.2 | .NET 3.5, .NET 4.0 | .NET 3.5, .NET 4.5.2 |
| Famiglia di sistemi operativi 3.x basata su Windows Server 2012 | .NET 4.5 | .NET 4.5.2 | .NET 4.5 | .NET 4.5.2 | 
| Famiglia di sistemi operativi 4.x basata su Windows Server 2012 R2 | .NET 4.5.1 | .NET 4.5.2 | .NET 4.5.1 | .NET 4.5.2 | 
 
Se si utilizza l'aggiornamento manuale del sistema operativo guest, in novembre vi saranno due versioni di questo sistema operativo per tutte le famiglie di sistemi operativi guest.

   • WA-GUEST-OS-4.26\_201511-01, WA-GUEST-OS-3.33\_201511-01, WA-GUEST-OS-2.45\_201511-01 (impostazione predefinita)

   Queste includono MSRC di novembre e i rollup di Windows di ottobre

   • WA-GUEST-OS-4.26\_201511-02, WA-GUEST-OS-3.33\_201511-02, WA-GUEST-OS-2.45\_201511-02

   Queste includono MSRC di novembre, i rollup di Windows di ottobre e .NET Framework aggiornato a .NET 4.5.2



Prima di eseguire l'aggiornamento, ecco alcune delle opzioni per convalidare il servizio cloud con .NET 4.5.2:

1. 	Distribuire un ruolo del servizio cloud di test alla versione 201511-02 ed eseguire la convalida di compatibilità delle applicazioni. 

2. 	Installare .NET 4.5.2 in un ruolo del servizio cloud di test ed eseguire la convalida di compatibilità delle applicazioni. Visitare [Installare .NET in un ruolo del servizio cloud] per le istruzioni.

3. 	Eseguire la convalida di compatibilità delle applicazioni su una macchina virtuale autonoma, locale o su una macchina virtuale di Azure con .NET 4.5.2 installato.




###### **15 ottobre 2015**
L’implementazione del sistema operativo guest di ottobre è iniziata oggi 15 ottobre 2015 e dovrebbe essere rilasciata il 13 novembre 2015.

Le versioni del sistema operativo guest 4.24, 3.31, 2.43 sono state rilasciate il 1 ottobre 2015.

###### **9 settembre 2015**
L’implementazione del sistema operativo guest di settembre è iniziata oggi 9 settembre 2015 e dovrebbe essere rilasciata l’8 ottobre 2015.

Le versioni del sistema operativo guest 4.23, 3.30, 2.42 sono state rilasciate il 9 settembre 2015.

###### **14 agosto 2015**
L’implementazione del sistema operativo guest di agosto è iniziata oggi 14 agosto 2015 e dovrebbe essere rilasciata l’11 settembre 2015.

###### **7 agosto 2015**
Le versioni del sistema operativo guest 4.22, 3.29, 2.41 sono state rilasciate il 7 agosto 2015.

###### **14 luglio 2015**
L’implementazione del sistema operativo guest di luglio è iniziata oggi 14 luglio 2015 e dovrebbe essere rilasciata il 14 agosto 2015.

Le versioni del sistema operativo guest 4.21, 3.28, 2.40 sono state rilasciate il 9 luglio 2015.

###### **15 giugno 2015**
L’implementazione del sistema operativo guest di giugno è iniziata oggi 15 giugno 2015 e dovrebbe essere rilasciata il 9 luglio 2015.

Le versioni del sistema operativo guest 4.20, 3.27, 2.39 sono state rilasciate il 12 giugno 2015.

###### **20 maggio 2015**
L’implementazione del sistema operativo guest di maggio è iniziata oggi 20 maggio 2015 e dovrebbe essere rilasciata il 12 giugno 2015.

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




## Informazioni sui rilasci del sistema operativo guest

In questa sezione sono elencate le versioni del sistema operativo guest attualmente supportate. Per le famiglie e le versioni del sistema operativo guest sono previste una data di rilascio, una data di disabilitazione e una data di scadenza. A partire dalla data di rilascio, è possibile selezionare manualmente una versione di sistema operativo guest nel portale di Azure classico. Un sistema operativo guest viene rimosso dal portale di Azure classico lo stesso giorno o dopo la relativa data di "disabilitazione". È quindi "in transizione", ma è supportato con capacità limitate di aggiornamento di una distribuzione. La data di scadenza è la data in cui è prevista la rimozione completa di una versione o di una famiglia dal sistema Azure. Alla data di scadenza i servizi cloud ancora in esecuzione in una versione verranno arrestati, eliminati o aggiornati in modo forzato a una versione più recente, come descritto in [Criteri relativi al supporto e al ritiro del sistema operativo guest Azure][retirepolicy].

Microsoft supporta almeno le due versioni più recenti di ogni famiglia del sistema operativo guest supportata. La data di disabilitazione di una versione di sistema operativo guest esistente può essere posticipata per garantire che almeno due versioni rilasciate rimangano sempre disponibili per la distribuzione.

> [AZURE.WARNING] Il ritiro della famiglia di sistemi operativi guest 1 è iniziato il 1 giugno 2013 e il completamento è previsto a breve. Non creare nuove installazioni e aggiornare quelle precedenti mediante questa famiglia di sistemi operativi guest. Per altre informazioni, vedere [Informazioni sul ritiro della famiglia di sistemi operativi guest Azure 1][fam1retire]


### Famiglia, versione e rilascio del sistema operativo guest
Le famiglie dei sistemi operativi guest sono basate sulle versioni rilasciate di Microsoft Windows Server e il sistema operativo guest è il sistema operativo sottostante in cui viene eseguito Servizi cloud di Azure. A ogni sistema operativo guest sono associati una famiglia, una versione e un numero di rilascio.

Una **famiglia di sistemi operativi guest** corrisponde a un rilascio del sistema operativo Windows Server su cui è basato un sistema operativo guest. Ad esempio, la famiglia 3 è basata su Windows Server 2012.

Con l'espressione **versione del sistema operativo guest** si intendono l'immagine della famiglia di sistemi operativi e le patch rilevanti di [Microsoft Security Response Center (MSRC)][msrc] disponibili al momento dell'introduzione della nuova versione del sistema operativo guest. È possibile che non siano incluse tutte le patch. I numeri iniziano da 0 e vengono incrementati di 1 a ogni aggiunta di un nuovo set di aggiornamenti. Gli zeri finali vengono visualizzati solo se sono importanti; ad esempio, la versione 2.10 è una versione diversa e successiva rispetto alla versione 2.1.

Con il termine **rilascio del sistema operativo guest** si intende un nuovo rilascio di una versione del sistema operativo guest. Il nuovo rilascio viene introdotto se durante la fase di test Microsoft rileva problemi che richiedono l'esecuzione di modifiche. L'ultimo rilascio sostituisce sempre quelli precedenti, che siano pubblici o meno. Il portale di Azure classico consente agli utenti di scaricare soltanto l'ultimo rilascio disponibile per una determinata versione. Le distribuzioni in cui si usa una versione precedente in genere non vengono forzate all'aggiornamento in base alla gravità del bug.

Nell'esempio seguente, 2 rappresenta la famiglia, 12 la versione e "rel2" il rilascio.

**Rilascio del sistema operativo guest** - 2.12 rel2

**Stringa di configurazione per questo rilascio** - WA-GUEST-OS-2.12\_201208-02

Nella stringa di configurazione per un sistema operativo guest sono contenute queste stesse informazioni, insieme a una data che indica quali patch di MSRC sono state prese in considerazione per il rilascio. In questo esempio, sono state incluse le patch di MSRC prodotte per Windows Server 2008 R2 fino ad agosto 2012. Vengono incluse solo le patch che si applicano specificatamente alla versione di Windows Server. Ad esempio, se una patch MSRC si applica a Microsoft Office, non verrà inclusa poiché il prodotto non fa parte dell'immagine di base di Windows Server.

## Rilasci

## Rilasci della famiglia 4
**Windows Server 2012 R2**

Supporta .NET 4.0, 4.5, 4.5.1, 4.5.2 (Nota 2)

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 4\.29 | WA-GUEST-OS-4.29\_201602-01 | Prevista: 9 marzo 2016 | Verrà aggiornata al momento del rilascio della versione 4.31 | Da definire |
| 4\.28 | WA-GUEST-OS-4.28\_201601-01 | 12 febbraio 2016 | Verrà aggiornata al momento del rilascio della versione 4.30 | Da definire |
| 4\.27 | WA-GUEST-OS-4.27\_201512-01 | 12 gennaio 2016 | Verrà aggiornata al momento del rilascio della versione 4.29 | Da definire |
| 4\.26 | WA-GUEST-OS-4.26\_201511-02 | 4 gennaio 2016 | 12 marzo 2016| Da definire |
| 4\.26 | WA-GUEST-OS-4.26\_201511-01 | 10 dicembre 2015 | 12 marzo 2016| Da definire |
| 4\.25 | WA-GUEST-OS-4.25\_201510-01 | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| 4\.24 | WA-GUEST-OS-4.24\_201509-01 | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| 4\.23 | WA-GUEST-OS-4.23\_201508-02 | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| 4\.22 | WA-GUEST-OS-4.22\_201507-02 | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| 4\.21 | WA-GUEST-OS-4.21\_201506-01 | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| 4\.20 | WA-GUEST-OS-4.20\_201505-02 | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| 4\.19 | WA-GUEST-OS-4.19\_201504-01 | 17 aprile 2015 | 9 agosto 2015 | Da definire |
| 4\.18 | WA-GUEST-OS-4.18\_201503-01 | 2 aprile 2015 | 12 luglio 2015 | 14 ottobre 2015 |
| 4\.17 | WA-GUEST-OS-4.17\_201502-01 | 9 marzo 2015 | 17 maggio 2015 | 14 ottobre 2015 |
| 4\.16 | WA-GUEST-OS-4.16\_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | 14 ottobre 2015 |
| 4\.15 | WA-GUEST-OS-4.15\_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | 14 ottobre 2015 |
| 4\.14 | WA-GUEST-OS-4.14\_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | 14 ottobre 2015 |
| 4\.13 | WA-GUEST-OS-4.13\_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | 14 ottobre 2015 |
| 4\.12 (Nota 1) | WA-GUEST-OS-4.12\_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 4\.11 (Nota 1) | WA-GUEST-OS-4.11\_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 4\.10 | WA-GUEST-OS-4.10\_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 4\.9 | WA-GUEST-OS-4.9\_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 4\.8 | WA-GUEST-OS-4.8\_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |

## Versioni della famiglia 3

**Windows Server 2012**

Supporta .NET 4.0, 4.5

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 3\.36 | WA-GUEST-OS-3.36\_201602-01 | Prevista: 9 marzo 2016 | Verrà aggiornata al momento del rilascio della versione 3.38 | Da definire |
| 3\.35 | WA-GUEST-OS-3.35\_201601-01 | 12 febbraio 2016 | Verrà aggiornata al momento del rilascio della versione 3.37 | Da definire |
| 3\.34 | WA-GUEST-OS-3.34\_201512-01 | 12 gennaio 2016 | Verrà aggiornata al momento del rilascio della versione 3.36 | Da definire |
| 3\.33 | WA-GUEST-OS-3.33\_201511-02 | 4 gennaio 2016 | 12 marzo 2016| Da definire |
| 3\.33 | WA-GUEST-OS-3.33\_201511-01 | 10 dicembre 2015 | 12 marzo 2016| Da definire |
| 3\.32 | WA-GUEST-OS-3.32\_201510-01 | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| 3\.31 | WA-GUEST-OS-3.31\_201509-01 | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| 3\.30 | WA-GUEST-OS-3.30\_201508-02 | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| 3\.29 | WA-GUEST-OS-3.29\_201507-02 | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| 3\.28 | WA-GUEST-OS-3.28\_201506-01 | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| 3\.27 | WA-GUEST-OS-3.27\_201505-02 | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| 3\.26 | WA-GUEST-OS-3.26\_201504-01 | 17 aprile 2015 | 9 agosto 2015 | Da definire |
| 3\.25 | WA-GUEST-OS-3.25\_201503-01 | 2 aprile 2015 | 12 luglio 2015 | 14 ottobre 2015 |
| 3\.24 | WA-GUEST-OS-3.24\_201502-01 | 9 marzo 2015 | 17 maggio 2015 | 14 ottobre 2015 |
| 3\.23 | WA-GUEST-OS-3.23\_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | 14 ottobre 2015 |
| 3\.22 | WA-GUEST-OS-3.22\_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | 14 ottobre 2015 |
| 3\.21 | WA-GUEST-OS-3.21\_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | 14 ottobre 2015 |
| 3\.20 | WA-GUEST-OS-3.20\_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | 14 ottobre 2015 |
| 3\.19 (Nota 1) | WA-GUEST-OS-3.19\_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 3\.18 (Nota 1) | WA-GUEST-OS-3.18\_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 3\.17 | WA-GUEST-OS-3.17\_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 3\.16 | WA-GUEST-OS-3.16\_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 3\.15 | WA-GUEST-OS-3.15\_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |


## Versioni della famiglia 2

**Windows Server 2008 R2 SP1**

Supporta .NET 3.5, 4.0

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 2\.48 | WA-GUEST-OS-2.48\_201602-01 | Prevista: 9 marzo 2016 | Verrà aggiornata al momento del rilascio della versione 2.50 | Da definire |
| 2\.47 | WA-GUEST-OS-2.47\_201601-01 | 12 febbraio 2016 | Verrà aggiornata al momento del rilascio della versione 2.49 | Da definire |
| 2\.46 | WA-GUEST-OS-2.46\_201512-01 | 12 gennaio 2016 | Verrà aggiornata al momento del rilascio della versione 2.48 | Da definire |
| 2\.45 | WA-GUEST-OS-2.45\_201511-02 | 4 gennaio 2016 | 12 marzo 2016| Da definire |
| 2\.45 | WA-GUEST-OS-2.45\_201511-01 | 10 dicembre 2015 | 12 marzo 2016| Da definire |
| 2\.44 | WA-GUEST-OS-2.44\_201510-01 | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| 2\.43 | WA-GUEST-OS-2.43\_201509-01 | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| 2\.42 | WA-GUEST-OS-2.42\_201508-02 | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| 2\.41 | WA-GUEST-OS-2.41\_201507-02 | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| 2\.40 | WA-GUEST-OS-2.40\_201506-01 | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| 2\.39 | WA-GUEST-OS-2.39\_201505-02 | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| 2\.38 | WA-GUEST-OS-2.38\_201504-01 | 17 aprile 2015 | 9 agosto 2015 | Da definire |
| 2\.37 | WA-GUEST-OS-2.37\_201503-01 | 2 aprile 2015 | 12 luglio 2015 | 14 ottobre 2015 |
| 2\.36 | WA-GUEST-OS-2.36\_201502-01 | 9 marzo 2015 | 17 maggio 2015 | 14 ottobre 2015 |
| 2\.35 | WA-GUEST-OS-2.35\_201501-01 | 29 gennaio 2015 | 2 maggio 2015 | 14 ottobre 2015 |
| 2\.34 | WA-GUEST-OS-2.34\_201412-01 | 14 gennaio 2015 | 9 aprile 2015 | 14 ottobre 2015 |
| 2\.33 | WA-GUEST-OS-2.33\_201411-01 | 11 novembre 2014 | 28 febbraio 2015 | 14 ottobre 2015 |
| 2\.32 | WA-GUEST-OS-2.32\_201410-01 | 3 novembre 2014 | 14 febbraio 2015 | 14 ottobre 2015 |
| 2\.31 (Nota 1) | WA-GUEST-OS-2.31\_201409-02 | 6 ottobre 2014 | 12 ottobre 2014 | 23 marzo 2015 |
| 2\.30 (Nota 1) | WA-GUEST-OS-2.30\_201408-02 | 25 agosto 2014 | 11 settembre 2014 | 23 marzo 2015 |
| 2\.29 | WA-GUEST-OS-2.29\_201407-01 | 18 luglio 2014 | 1 dicembre 2014 | 23 marzo 2015 |
| 2\.28 | WA-GUEST-OS-2.28\_201406-01 | 16 giugno 2014 | 10 novembre 2014 | 23 marzo 2015 |
| 2\.27 | WA-GUEST-OS-2.27\_201405-01 | 1 giugno 2014 | 1 agosto 2014 | 23 marzo 2015 |




### Versioni della famiglia 1
La **FAMIGLIA 1** è stata [ritirata][fam1retire].

#### Nota 1
Le versioni di agosto e settembre 2014 sono state distribuite parzialmente a causa di problemi relativi a una [patch MSRC][MS14-046]. A causa di questo problema non è possibile installare manualmente .NET 3.5.1 nelle famiglie di sistemi operativi guest 3 e 4. La versione è stata disabilitata a titolo precauzionale affinché i clienti non potessero selezionarla manualmente.

#### Nota 2
Alla data del 19 settembre 2014, .NET 4.5.2 non è stato specificamente testato nel sistema operativo guest Azure, ma il sistema operativo guest è essenzialmente equivalente a Windows Server. Le stesse regole di compatibilità che si applicano al prodotto Windows Server si applicano quindi anche alle famiglie di sistemi operativi guest equivalenti. Se si verifica un'eccezione a questi criteri, contattare il [supporto di Azure][azuresupport]. Microsoft si impegnerà a risolvere il problema. [Pacchetto di installazione manuale per .NET 4.5.2][net install pkg].

## Aggiornamenti di MSRC inclusi nel sistema operativo guest
L'elenco di patch incluse con il rilascio del sistema operativo di ogni mese è disponibile [qui][patches].

## Supporto SDK

In questa tabella viene indicata la famiglia di sistemi operativi guest compatibile con le diverse versioni di Azure SDK. Per altre informazioni non contenute in questa tabella, vedere [Informazioni su supporto e ritiro di Azure SDK per .NET e API][retire policy sdk]. Le informazioni contenute in questo elenco sostituiscono quelle seguenti.

> [AZURE.IMPORTANT] Per garantire il corretto funzionamento del servizio, è necessario distribuirlo nella versione del sistema operativo guest compatibile con quella di Azure SDK usata per sviluppare il servizio. In caso contrario, nel cloud del servizio distribuito potrebbero verificarsi degli errori che non erano evidenti nell'ambiente di sviluppo.

| Famiglia del sistema operativo guest | Versioni SDK supportate |
| --------------- | ---------------------- |
| 4 | Versione 2.1 e successive |
| 3 | Versione 1.8 e successive |
| 2 | Versione 1.3 e successive |
| 1 | Versione 1.0 e successive |


## Processo di aggiornamento del sistema operativo guest
In questa pagina vengono fornite informazioni sui prossimi rilasci del sistema operativo guest. I clienti hanno indicato di voler sapere quando viene rilasciata una versione poiché i ruoli del servizio cloud vengono riavviati se impostati per l'aggiornamento automatico. Le versioni del sistema operativo guest vengono in genere rilasciate almeno 5 giorni dopo il rilascio dell'aggiornamento di MSRC che avviene il secondo martedì di ogni mese. Le nuove versioni includono tutte le patch importanti di MSRC per ogni famiglia del sistema operativo guest.

Gli aggiornamenti di Microsoft Azure vengono rilasciati costantemente. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da una serie di fattori che sono troppo numerosi per essere elencati in questa pagina. Inoltre, Azure viene eseguito su centinaia di migliaia di computer. Ciò significa che è impossibile specificare una data e un'ora esatte del riavvio dei ruoli. Il [feed RSS di aggiornamento del sistema operativo guest][rss] verrà aggiornato con le informazioni più recenti disponibili, ma tenere presente che i tempi sono approssimativi. Siamo consapevoli del fatto che questo rappresenta un problema per i clienti e stiamo preparando un piano per limitare o impostare una tempistica per i riavvii.

Quando viene pubblicato un nuovo rilascio del sistema operativo guest, la propagazione completa in Azure può richiedere tempo. Man mano che i servizi vengono aggiornati al nuovo sistema operativo guest, vengono riavviati rispettando i domini di aggiornamento. Un rilascio sarà innanzitutto fornito ai servizi per cui sono stati impostati gli aggiornamenti automatici. Dopo l'aggiornamento, la nuova versione del sistema operativo guest sarà elencata per il servizio nel portale di Azure classico. Durante questo periodo possono essere introdotti dei nuovi rilasci. Alcune versioni potrebbero essere distribuite per periodi più lunghi ed è possibile che i riavvii di aggiornamenti automatici non si verifichino per molte settimane dopo la data di rilascio ufficiale. Quando un sistema operativo guest diventa disponibile, è possibile scegliere esplicitamente tale versione dal portale o nel file di configurazione.

Per moltissime informazioni importanti sui riavvii e sui puntatori ad altre informazioni sui dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post del blog su MSDN relativo [ai riavvii dell'istanza del ruolo dovuti ad aggiornamenti del sistema operativo][restarts].

Se si aggiorna manualmente il sistema operativo guest, leggere le informazioni importanti seguenti sul [supporto e sul ritiro del sistema operativo guest][retirepolicy].


## Criteri relativi al supporto e al ritiro del sistema operativo guest
I criteri relativi al supporto e al ritiro del sistema operativo guest sono descritti [qui][retirepolicy].
 
## Archivio notizie

###### **11 novembre 2014**

La versione di novembre (4.14, 3.21 e 2.33) è stata distribuita l'11 novembre. Questo aggiornamento è stato distribuito tramite push in anticipo perché include l'aggiornamento [MSRC Bollettino Microsoft sulla sicurezza MS14-066 - Critico][MS14-066]. È consigliabile riavviare nei prossimi giorni i ruoli Web e di lavoro inclusi nell'aggiornamento automatico per ricevere la correzione.

###### **10 novembre 2014**
La data di disabilitazione della versione di ottobre (4.13, 3.20 e 2.32) è stata aggiornata in base ai commenti e suggerimenti dei clienti. La versione verrà sempre disabilitata una volta trascorsi almeno due mesi dalla data di rilascio.

###### **4 novembre 2014**
La versione di ottobre (4.13, 3.20 e 2.32) è stata distribuita il 4 novembre 2014. Include la patch MSRC che causava problemi nelle versioni di agosto e settembre. Per risolvere questo problema, la versione di ottobre include .NET 3.5 e 3.5.1 che sono preinstallati, ma non abilitati. Gli script che tentano di installare .NET 3.5 o 3.5.1 la abiliteranno e restituiranno un messaggio di operazione completata per l'installazione di .NET; inoltre, eviteranno il riprodursi del problema di installazione completa causato dalla patch MSRC.

**20 ottobre 2014. Aggiornata il 4 novembre 2014.** La versione di settembre (4.12, 3.19, 2.31 e 1.39) è stata distribuita parzialmente a causa di problemi relativi alla stessa [patch MSRC MS14-046][MS14-046] che genera errori durante il tentativo di installare .NET 3.5 o 3.5.1 nella famiglia di sistemi operativi guest 3 o 4. .NET 3.5.x NON è ufficialmente supportato in entrambe le famiglie, ma Microsoft sta tentando di risolvere il problema relativo a questa modifica del comportamento, perché influisce su alcune installazioni dei clienti e non era stata annunciata. Le date di disabilitazione dei sistemi operativi guest precedenti (giugno e luglio) verranno posticipate in modo da rendere disponibili almeno due versioni complete di sistemi operativi guest e garantirne il supporto. Una soluzione al problema di installazione di .NET è presente nella versione di ottobre 2014.

Nella versione di ottobre sono inclusi .NET 3.5 e 3.5.1 preinstallati ma disabilitati, oltre alla patch MSRC indicata in precedenza. Gli script che tentano di installare .NET 3.5 o 3.5.1 la abiliteranno e restituiranno un messaggio di operazione completata per l'installazione di .NET; inoltre, eviteranno il riprodursi del problema di installazione causato dalla patch MSRC.

A seguito dell'implementazione parziale delle ultime due versioni, gli utenti che hanno abilitato l'aggiornamento automatico o che hanno implementato nuove installazioni potrebbero avere installato una di queste versioni del sistema operativo guest. Nella tabella seguente sono riportate le versioni del sistema operativo guest che consentono l'installazione di .NET 3.5 o 3.5.1 nelle famiglie 3 e 4. Attualmente, se una versione consente l'installazione significa che NON è installata la patch MSRC MS14-046.

| Versione sistema operativo | Può installare .NET 3.5 | Include la patch MSRC [MS14-046][] |
| --- | --- | --- |
| Tutte le versioni successive del sistema operativo guest | Sì | Sì |
| WA-GUEST-OS-4.13\_201410-01 | Sì | Sì |
| WA-GUEST-OS-4.12\_201409-02 | No | Sì |
| WA-GUEST-OS-4.12\_201409-01 | No | Sì |
| WA-GUEST-OS-4.11\_201408-02 | Sì | No |
| WA-GUEST-OS-4.11\_201408-01 | No | Sì |
| WA-GUEST-OS-4.10\_201407-01 | Sì | No |

**20 ottobre 2014**. Poiché le versioni di agosto e settembre sono state distribuite solo parzialmente, è importante tenere presente quanto segue:

1. Le modifiche apportate alla crittografia descritte in Differenze tra il sistema operativo guest Azure e Windows Server predefinito non sono state distribuite all'intero ambiente Azure. I clienti non inclusi nelle versioni di agosto o settembre riceveranno queste modifiche nella versione di ottobre. 

2. I sistemi operativi guest di agosto e settembre sono stati disabilitati nel portale di Azure classico. e non è possibile sceglierli manualmente. La disabilitazione ha lo scopo di evitare problemi che possono verificarsi se si seleziona questa versione del sistema operativo guest.

3. Le date di disabilitazione di alcune versioni precedenti sono state posticipate per garantire la continuità della disponibilità nel portale e il supporto per almeno due versioni di sistemi operativi guest in ogni famiglia.

## Archivio delle versioni

### Famiglia 4

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 4\.7 | WA-GUEST-OS-4.7\_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 4\.6 | WA-GUEST-OS-4.6\_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 4\.5 | WA-GUEST-OS-4.5\_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 4\.4 | WA-GUEST-OS-4.4\_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 4\.3. | WA-GUEST-OS-4.3\_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 4\.2 | WA-GUEST-OS-4.2\_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 4\.1 | WA-GUEST-OS-4.1\_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 4\.0 rel3 | WA-GUEST-OS-4.0\_201309-03 | 9 ottobre 2013 Resa pubblica il 18 ottobre. | N/D | 14 maggio 2014 |
 


### Famiglia 3

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 3\.14 | WA-GUEST-OS-3.14\_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 3\.13 | WA-GUEST-OS-3.13\_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 3\.12 | WA-GUEST-OS-3.12\_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 3\.11 | WA-GUEST-OS-3.11\_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 3\.10 | WA-GUEST-OS-3.10\_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 3\.9 | WA-GUEST-OS-3.9\_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 3\.8 | WA-GUEST-OS-3.8\_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 3\.7 rel3 | WA-GUEST-OS-3.7\_201309-03 | 9 ottobre 2013 | N/D | 14 maggio 2014 |
| 3\.7 rel1 | WA-GUEST-OS-3.7\_201309-01 | 23 settembre 2013 | N/D | 14 maggio 2014 |

### Famiglia 2

| Versione sistema operativo guest | Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 2\.26 | WA-GUEST-OS-2.26\_201404-01 | 2 maggio 2014 | 2 luglio 2014 | 18 agosto 2014 |
| 2\.25 | WA-GUEST-OS-2.25\_201403-01 | 28 marzo 2014 | 9 giugno 2014 | 18 agosto 2014 |
| 2\.24 | WA-GUEST-OS-2.24\_201402-01 | 21 marzo 2014 | 21 maggio 2014 | 18 agosto 2014 |
| 2\.23 | WA-GUEST-OS-2.23\_201401-01 | 8 febbraio 2014 | 8 aprile 2014 | 14 maggio 2014 |
| 2\.22 | WA-GUEST-OS-2.22\_201312-01 | 6 gennaio 2014 | 6 marzo 2014 | 14 maggio 2014 |
| 2\.21 | WA-GUEST-OS-2.21\_201311-01 | 12 dicembre 2013 | 12 febbraio 2014 | 14 maggio 2014 |
| 2\.20 | WA-GUEST-OS-2.20\_201310-01 | 29 ottobre 2013 | N/D | 14 maggio 2014 |
| 2\.19 rel3 | WA-GUEST-OS-2.19\_201309-03 | 9 ottobre 2013 | N/D | 14 maggio 2014 |
| 2\.19 rel1 | WA-GUEST-OS-2.19\_201309-01 | 23 settembre 2013 | N/D | 14 maggio 2014 |

[Installare .NET in un ruolo del servizio cloud]: https://azure.microsoft.com/it-IT/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Impostazioni di aggiornamento del sistema operativo guest Azure]: cloud-services-how-to-configure.md
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
 

<!---HONumber=AcomDC_0224_2016-->
