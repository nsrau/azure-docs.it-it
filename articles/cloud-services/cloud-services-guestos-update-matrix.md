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
   ms.date="04/17/2016"
   ms.author="yuemlu"/>

# Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK
Fornisce informazioni aggiornate sui rilasci del sistema operativo guest Azure più recente per i servizi cloud. Queste informazioni sono utili per pianificare il percorso di aggiornamento prima che un sistema operativo guest venga disabilitato. Se si configurano i ruoli per l'uso degli aggiornamenti *automatici* del sistema operativo guest come descritto in [Impostazioni di aggiornamento del sistema operativo guest Azure][], non è indispensabile leggere questa pagina.

> [AZURE.IMPORTANT] Questa pagina è applicabile ai ruoli Web e ai ruoli di lavoro dei servizi cloud in esecuzione su un sistema operativo guest. **Non si applica** alle macchine virtuali IaaS.

<!-- -->

> [AZURE.TIP] Sottoscrivere il [feed RSS di aggiornamento del sistema operativo guest][rss] per ricevere notifiche più tempestive su tutte le modifiche al sistema operativo guest.

Non si è certi di quale sia il sistema operativo guest o di come funzionano i rilasci del sistema operativo guest? Leggere [questa](#how-it-works) sezione.

## Novità e aggiornamenti

###### **18 aprile 2016**
L'implementazione del sistema operativo guest di aprile inizierà il 18 aprile 2016 e dovrebbe essere rilasciata il 12 maggio 2016.

###### **14 marzo 2016**
L’implementazione del sistema operativo guest di marzo è iniziata oggi 14 marzo 2016 e dovrebbe essere rilasciata l’8 aprile 2016.

###### **22 febbraio 2016**
L'implementazione del sistema operativo guest di febbraio è iniziata il 22 febbraio 2016 e dovrebbe essere rilasciata il 9 marzo 2016.

###### **18 gennaio 2016**
L'implementazione del sistema operativo guest di gennaio è iniziata il 18 gennaio 2016 e dovrebbe essere rilasciata il 12 febbraio 2016.

###### **4 gennaio 2016**
Il sistema operativo guest del 2 novembre 2015 è stato rilasciato per la distribuzione il 4 gennaio 2016. Questa versione del sistema operativo non è impostata come sistema operativo predefinito per l'aggiornamento automatico, quindi i tempi di provisioning della distribuzione del sistema operativo guest alla versione del 2 novembre 2015 sono leggermente più lunghi.

## Rilasci

## Versioni della famiglia 4
**Windows Server 2012 R2**

Supporta .NET 4.0, 4.5, 4.5.1, 4.5.2 (Nota 2)

>[AZURE.NOTE] La date contrassegnate da * sono soggette a modifiche.

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ------------------------------ | --------------- | ------------- | ---- |
| WA-GUEST-OS-4.31\_201604-01 | 12 maggio 2016* | Dopo la versione 4.33 | Da definire |
| WA-GUEST-OS-4.30\_201603-01 | 7 aprile 2016 | Dopo la versione 4.32 | Da definire |
| WA-GUEST-OS-4.29\_201602-02 | 12 marzo 2016 | Dopo la versione 4.31 | Da definire |
| WA-GUEST-OS-4.28\_201601-01 | 12 febbraio 2016 | 7 maggio 2016 | Da definire | 
| WA-GUEST-OS-4.27\_201512-01 | 12 gennaio 2016 | 12 aprile 2016 | Da definire |
| ~~WA-GUEST-OS-4.26\_201511-02~~ | 4 gennaio 2016 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-4.26\_201511-01~~ | 10 dicembre 2015 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-4.25\_201510-01~~ | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| ~~WA-GUEST-OS-4.24\_201509-01~~ | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| ~~WA-GUEST-OS-4.23\_201508-02~~ | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| ~~WA-GUEST-OS-4.22\_201507-02~~ | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| ~~WA-GUEST-OS-4.21\_201506-01~~ | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| ~~WA-GUEST-OS-4.20\_201505-02~~ | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| ~~WA-GUEST-OS-4.19\_201504-01~~ | 17 aprile 2015 | 9 agosto 2015 | Da definire |

## Versioni della famiglia 3

**Windows Server 2012**

Supporta .NET 4.0, 4.5

>[AZURE.NOTE] La date contrassegnate da * sono soggette a modifiche.

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ------------------------------ | -------------- | ------------- | --- |
| WA-GUEST-OS-3.38\_201604-01 | 12 maggio 2016* | Dopo la versione 3.40 | Da definire |
| WA-GUEST-OS-3.37\_201603-01 | 7 aprile 2016 | Dopo la versione 3.39 | Da definire |
| WA-GUEST-OS-3.36\_201602-02 | 12 marzo 2016 | Dopo la versione 3.38 | Da definire |
| WA-GUEST-OS-3.35\_201601-01 | 12 febbraio 2016 | 7 maggio 2016 | Da definire |
| WA-GUEST-OS-3.34\_201512-01 | 12 gennaio 2016 | 12 aprile 2016 | Da definire |
| ~~WA-GUEST-OS-3.33\_201511-02~~ | 4 gennaio 2016 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-3.33\_201511-01~~ | 10 dicembre 2015 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-3.32\_201510-01~~ | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| ~~WA-GUEST-OS-3.31\_201509-01~~ | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| ~~WA-GUEST-OS-3.30\_201508-02~~ | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| ~~WA-GUEST-OS-3.29\_201507-02~~ | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| ~~WA-GUEST-OS-3.28\_201506-01~~ | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| ~~WA-GUEST-OS-3.27\_201505-02~~ | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| ~~WA-GUEST-OS-3.26\_201504-01~~ | 17 aprile 2015 | 9 agosto 2015 | Da definire |


## Versioni della famiglia 2

**Windows Server 2008 R2 SP1**

Supporta .NET 3.5, 4.0

>[AZURE.NOTE] La date contrassegnate da * sono soggette a modifiche.

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| ------------------------------ | ------------- | ------------  | --- |
| WA-GUEST-OS-2.50\_201604-01 | 12 maggio 2016* | Dopo la versione 2.52 | Da definire |
| WA-GUEST-OS-2.49\_201603-01 | 7 aprile 2016 | Dopo la versione 2.51 | Da definire |
| WA-GUEST-OS-2.48\_201602-02 | 12 marzo 2016 | Dopo la versione 2.50 | Da definire |
| WA-GUEST-OS-2.47\_201601-01 | 12 febbraio 2016 | 7 maggio 2016 | Da definire |
| WA-GUEST-OS-2.46\_201512-01 | 12 gennaio 2016 | 12 aprile 2016 | Da definire |
| ~~WA-GUEST-OS-2.45\_201511-02~~ | 4 gennaio 2016 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-2.45\_201511-01~~ | 10 dicembre 2015 | 12 marzo 2016 | Da definire |
| ~~WA-GUEST-OS-2.44\_201510-01~~ | 6 novembre 2015 | 12 febbraio 2016 | Da definire |
| ~~WA-GUEST-OS-2.43\_201509-01~~ | 1 ottobre 2015 | 10 gennaio 2016 | Da definire |
| ~~WA-GUEST-OS-2.42\_201508-02~~ | 9 settembre 2015 | 6 dicembre 2015 | Da definire |
| ~~WA-GUEST-OS-2.41\_201507-02~~ | 7 agosto 2015 | 1 novembre 2015 | Da definire |
| ~~WA-GUEST-OS-2.40\_201506-01~~ | 9 luglio 2015 | 9 ottobre 2015 | Da definire |
| ~~WA-GUEST-OS-2.39\_201505-02~~ | 12 giugno 2015 | 7 settembre 2015 | Da definire |
| ~~WA-GUEST-OS-2.38\_201504-01~~ | 17 aprile 2015 | 9 agosto 2015 | Da definire |

## Patch di aggiornamento MSRC
L'elenco di patch incluse con il rilascio del sistema operativo di ogni mese è disponibile [qui][patches].

## Supporto SDK

Anche se i [criteri di ritiro di Azure SDK][retire policy sdk] indicano che solo le versioni successive alla 2.2 sono supportate, determinate famiglie di sistemi operativi guest consentono di usare versioni precedenti. È sempre necessario usare l’SDK più recente supportato.

| Famiglia del sistema operativo guest | Versioni dell’SDK compatibili |
| --------------- | ----------------------- |
| 4 | Versione 2.1+ |
| 3 | Versione 1.8+ |
| 2 | Versione 1.3+ |
| 1 | Versione 1.0+ |

## Informazioni sui rilasci del sistema operativo guest
Esistono tre date importanti per le versioni dei sistemi operativi guest: la data di **rilascio**, la data di **disabilitazione** e la data di **scadenza**. Un sistema operativo guest è considerato disponibile quando è disponibile sul portale e può essere selezionato come sistema operativo guest di destinazione. Quando un sistema operativo guest raggiunge la data di **disabilitazione**, viene rimosso da Azure. Tuttavia qualsiasi servizio cloud che abbia come obiettivo quel sistema operativo guest funzionerà normalmente.

La finestra temporale fra la data di **disabilitazione** e la data di **scadenza** offre un margine di tempo che facilita la transizione da un sistema operativo guest a un altro più recente. Se si usa l'opzione *automatico* come sistema operativo guest, la versione usata sarà sempre la più recente e non ci si dovrà preoccupare della scadenza.

Quando si supera la data di **scadenza**, qualsiasi servizio cloud che usa ancora quel sistema operativo guest verrà arrestato, eliminato o costretto all'aggiornamento. Altre informazioni sui criteri di ritiro sono disponibili [qui][retirepolicy].

## Spiegazione delle famiglie e delle versioni del sistema operativo guest
Le famiglie dei sistemi operativi guest sono basate sulle versioni rilasciate di Microsoft Windows Server e il sistema operativo guest è il sistema operativo sottostante in cui viene eseguito Servizi cloud di Azure. A ogni sistema operativo guest sono associati una famiglia, una versione e un numero di rilascio.

- **Famiglia di sistemi operativi guest** Una versione del sistema operativo Windows Server sulla quale è basato un sistema operativo guest. Ad esempio, la *famiglia 3* è basata su Windows Server 2012.

- **Versione del sistema operativo guest** Specifica per l'immagine di una famiglia di sistemi operativi guest e le patch pertinenti di [Microsoft Security Response Center (MSRC)][msrc] che sono disponibili al momento dell'introduzione della nuova versione del sistema operativo guest. È possibile che non siano incluse tutte le patch.

    I numeri iniziano da 0 e vengono incrementati di 1 a ogni aggiunta di un nuovo set di aggiornamenti. Gli zeri finali vengono visualizzati solo se sono importanti; ad esempio, la versione 2.10 è una versione diversa e successiva rispetto alla versione 2.1.

- **Rilascio del sistema operativo guest** Un nuovo rilascio di una versione del sistema operativo guest. Il nuovo rilascio viene introdotto se durante la fase di test Microsoft rileva problemi che richiedono l'esecuzione di modifiche. L'ultimo rilascio sostituisce sempre quelli precedenti, che siano pubblici o meno. Il portale di Azure classico consente agli utenti di scaricare soltanto l'ultimo rilascio disponibile per una determinata versione. Le distribuzioni in cui si usa una versione precedente in genere non vengono forzate all'aggiornamento in base alla gravità del bug.

Nell'esempio seguente, 2 rappresenta la famiglia, 12 la versione e "rel2" il rilascio.

**Rilascio del sistema operativo guest** - 2.12 rel2

**Stringa di configurazione per questo rilascio** - WA-GUEST-OS-2.12\_201208-02

Nella stringa di configurazione per un sistema operativo guest sono contenute queste stesse informazioni, insieme a una data che indica quali patch di MSRC sono state prese in considerazione per il rilascio. In questo esempio, sono state incluse le patch di MSRC prodotte per Windows Server 2008 R2 fino ad agosto 2012. Vengono incluse solo le patch che si applicano specificatamente alla versione di Windows Server. Ad esempio, se una patch MSRC si applica a Microsoft Office, non verrà inclusa poiché il prodotto non fa parte dell'immagine di base di Windows Server.

## Processo di aggiornamento del sistema operativo guest
In questa pagina vengono fornite informazioni sui prossimi rilasci del sistema operativo guest. I clienti hanno indicato di voler sapere quando viene rilasciata una versione poiché i ruoli del servizio cloud vengono riavviati se impostati per l'aggiornamento automatico. Le versioni del sistema operativo guest vengono in genere rilasciate almeno 5 giorni dopo il rilascio dell'aggiornamento di MSRC che avviene il secondo martedì di ogni mese. Le nuove versioni includono tutte le patch importanti di MSRC per ogni famiglia del sistema operativo guest.

Gli aggiornamenti di Microsoft Azure vengono rilasciati costantemente. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da una serie di fattori che sono troppo numerosi per essere elencati in questa pagina. Inoltre, Azure viene eseguito su centinaia di migliaia di computer. Ciò significa che è impossibile specificare una data e un'ora esatte del riavvio dei ruoli. Il [feed RSS di aggiornamento del sistema operativo guest][rss] verrà aggiornato con le informazioni più recenti disponibili, ma tenere presente che i tempi sono approssimativi. Siamo consapevoli del fatto che questo rappresenta un problema per i clienti e stiamo preparando un piano per limitare o impostare una tempistica per i riavvii.

Quando viene pubblicato un nuovo rilascio del sistema operativo guest, la propagazione completa in Azure può richiedere tempo. Man mano che i servizi vengono aggiornati al nuovo sistema operativo guest, vengono riavviati rispettando i domini di aggiornamento. Un rilascio sarà innanzitutto fornito ai servizi per cui sono stati impostati gli aggiornamenti automatici. Dopo l'aggiornamento, la nuova versione del sistema operativo guest sarà elencata per il servizio nel portale di Azure classico. Durante questo periodo possono essere introdotti dei nuovi rilasci. Alcune versioni potrebbero essere distribuite per periodi più lunghi ed è possibile che i riavvii di aggiornamenti automatici non si verifichino per molte settimane dopo la data di rilascio ufficiale. Quando un sistema operativo guest diventa disponibile, è possibile scegliere esplicitamente tale versione dal portale o nel file di configurazione.

Per moltissime informazioni importanti sui riavvii e sui puntatori ad altre informazioni sui dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post del blog su MSDN relativo [ai riavvii dell'istanza del ruolo dovuti ad aggiornamenti del sistema operativo][restarts].

Se si aggiorna manualmente il sistema operativo guest, leggere le informazioni importanti seguenti sul [supporto e sul ritiro del sistema operativo guest][retirepolicy].


## Criteri relativi al supporto e al ritiro del sistema operativo guest
I criteri relativi al supporto e al ritiro del sistema operativo guest sono descritti [qui][retirepolicy].

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/it-IT/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Impostazioni di aggiornamento del sistema operativo guest Azure]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
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
 

<!---HONumber=AcomDC_0420_2016-->