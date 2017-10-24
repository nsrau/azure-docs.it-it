---
title: "Informazioni sulle versioni più recenti del sistema operativo guest Azure | Documentazione Microsoft"
description: "Notizie sui rilasci più recenti e sulla compatibilità SDK per il sistema operativo guest di Servizi cloud di Azure."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/6/2017
ms.author: raiye
ms.openlocfilehash: 7d3541e6c437212139553d975fa756084da4f757
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK
Fornisce informazioni aggiornate sui rilasci del sistema operativo guest Azure più recente per i servizi cloud. Queste informazioni sono utili per pianificare il percorso di aggiornamento prima che un sistema operativo guest venga disabilitato. Se si configurano i ruoli per l'uso degli aggiornamenti *automatici* del sistema operativo guest come descritto in [Impostazioni di aggiornamento del sistema operativo guest di Azure][Azure Guest OS Update Settings], non è indispensabile leggere questa pagina.

> [!IMPORTANT]
> Questa pagina è applicabile ai ruoli Web e ai ruoli di lavoro dei servizi cloud in esecuzione su un sistema operativo guest. **Non si applica** alle macchine virtuali IaaS.
>
>


> [!TIP]
>  Sottoscrivere il [feed RSS di aggiornamento del sistema operativo guest] per ricevere notifiche più tempestive su tutte le modifiche al sistema operativo guest.
>
>

> [!IMPORTANT]
> A partire dall'implementazione di novembre, solo le due versioni più recenti del sistema operativo Guest saranno supportate e disponibili nel portale di Azure.
>
>

Non si è certi di quale sia il sistema operativo guest o di come funzionano i rilasci del sistema operativo guest? Leggere [questa](#how-it-works) sezione.

## <a name="news-updates"></a>Novità e aggiornamenti


###### <a name="october-6-2017"></a>**6 ottobre 2017**
È stato rilasciato il sistema operativo guest di settembre. Per questa versione di settembre di Windows Server 2016 netfx3 è abilitata per impostazione predefinita. I clienti devono aggiungere "dism /online /disable-feature /featurename:netfx3" in OnStart se il flusso di lavoro richiede l'esecuzione di un'app .NET 2.x con runtime 4.x o se è stata eseguita un'app .NET 2.x, gestito un errore e quindi eseguita un'app .NET 4.x.

###### <a name="september-14-2017"></a>**14 settembre 2017**
L'implementazione del sistema operativo guest di settembre è iniziata il 14 settembre e dovrebbe essere rilasciata il 9 ottobre.

###### <a name="august-24-2017"></a>**24 agosto 2017**
È stato rilasciato il sistema operativo guest di agosto.

###### <a name="august-3-2017"></a>**3 agosto 2017**
È stato rilasciato il sistema operativo guest di luglio.

###### <a name="july-19-2017"></a>**19 luglio 2017**
L'implementazione del sistema operativo guest di luglio è iniziata il 19 luglio e il rilascio è previsto per l'8 agosto.

###### <a name="july-7-2017"></a>**7 luglio 2017**
È stato rilasciato il sistema operativo guest di giugno.

###### <a name="june-16-2017"></a>**16 giugno 2017**
L'implementazione del sistema operativo guest di giugno è iniziata il 16 giugno e il rilascio è previsto per l'11 luglio.

###### <a name="june-5-2017"></a>**5 giugno 2017**
È stato rilasciato il sistema operativo guest di maggio.

###### <a name="may-17-2017"></a>**17 maggio 2017**
A causa di un bug di sicurezza, le versioni del sistema operativo di dicembre 2016 e gennaio 2017 seguenti verranno disabilitate poiché non presentano le [correzioni] del portale: WA-GUEST-OS-5.4_201612-01, WA-GUEST-OS-4.39_201612-01, WA-GUEST-OS-3.46_201612-01, WA-GUEST-OS-2.59_201701-01

###### <a name="may-12-2017"></a>**12 maggio 2017**
L'implementazione del sistema operativo guest di maggio è iniziata il 12 maggio e il rilascio è previsto per l'13 giugno.

###### <a name="april-18-2017"></a>**18 aprile 2017**
L'implementazione del sistema operativo guest di aprile è iniziata il 18 aprile e il rilascio è previsto per il 9 maggio.


## <a name="releases"></a>Rilasci
## <a name="family-5-releases"></a>Versioni della famiglia 5
**Windows Server 2016**

Versione .NET Framework installata: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> La date contrassegnate da * sono soggette a modifiche.
>
> La password RDP per la famiglia di sistemi operativi 5 deve contenere almeno 10 caratteri.
>

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.11_201709-01 |6 ottobre 2017 |Post 5.13 |Da definire |
| WA-GUEST-OS-5.10_201708-01 |24 agosto 2017 |Dopo la versione 5.12 |Da definire |
| WA-GUEST-OS-5.9_201707-01 |3 agosto 2017 |Post 5.11 |Da definire |
|~~WA-GUEST-OS-5.8_201706-01~~ |7 luglio 2017 |6 ottobre 2017 |Da definire |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 giugno 2017 |24 agosto 2017 |Da definire |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 maggio 2017 |3 agosto 2017 |Da definire |
|~~WA-GUEST-OS-5.5_201703-01~~ |10 aprile 2017 |7 luglio 2017 |Da definire |
|~~WA-GUEST-OS-5.4_201612-01~~ |10 gennaio 2017 |5 giugno 2017|Da definire |
|~~WA-GUEST-OS-5.3_201611-01~~ |14 dicembre 2016 |9 maggio 2017 |Da definire |
|~~WA-GUEST-OS-5.2_201610-02~~ |1 novembre 2016 |10 aprile 2017 |Da definire |

## <a name="family-4-releases"></a>Versioni della famiglia 4
**Windows Server 2012 R2**

Supporta .NET 4.0, 4.5, 4.5.1 e 4.5.2

> [!NOTE]
> La date contrassegnate da * sono soggette a modifiche.
>
>

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.46_201709-01 |6 ottobre 2017 |Post 4.48 |Da definire |
| WA-GUEST-OS-4.45_201708-01 |24 agosto 2017 |Dopo la versione 4.47 |Da definire |
| WA-GUEST-OS-4.44_201707-01 |3 agosto 2017 |Post 4.46 |Da definire |
|~~WA-GUEST-OS-4.43_201706-01~~ |7 luglio 2017 |6 ottobre 2017 |Da definire |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 giugno 2017 |24 agosto 2017 |Da definire |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 maggio 2017 |3 agosto 2017 |Da definire |
|~~WA-GUEST-OS-4.40_201703-01~~ |10 aprile 2017 |7 luglio 2017 |Da definire |
|~~WA-GUEST-OS-4.39_201612-01~~ |10 gennaio 2017 |5 giugno 2017 |Da definire |
|~~WA-GUEST-OS-4.38_201611-01~~ |14 dicembre 2016 |9 maggio 2017 |Da definire |
|~~WA-GUEST-OS-4.37_201610-02~~ |16 novembre 2016 |10 aprile 2017 |Da definire |
|~~WA-GUEST-OS-4.36_201609-01~~ |13 ottobre 2016 |14 gennaio 2017 |Da definire |
|~~WA-GUEST-OS-4.35_201608-01~~ |13 settembre 2016 |16 dicembre 2016 |Da definire |
|~~WA-GUEST-OS-4.34_201607-01~~ |8 agosto 2016 |13 novembre 2016 |Da definire |


## <a name="family-3-releases"></a>Versioni della famiglia 3
**Windows Server 2012**

Supporta .NET 4.0, 4.5, 4.5.1 e 4.5.2

> [!NOTE]
> La date contrassegnate da * sono soggette a modifiche.
>
>

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.53_201709-01 |6 ottobre 2017 |Post 3.55 |Da definire |
| WA-GUEST-OS-3.52_201708-01 |24 agosto 2017 |Dopo la versione 3.54 |Da definire |
| WA-GUEST-OS-3.51_201707-01 |3 agosto 2017 |Post 3.53 |Da definire |
|~~WA-GUEST-OS-3.50_201706-01~~ |7 luglio 2017 |6 ottobre 2017 |Da definire |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 giugno 2017 |24 agosto 2017 |Da definire |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 maggio 2017 |3 agosto 2017 |Da definire |
|~~WA-GUEST-OS-3.47_201703-01~~ |10 aprile 2017 |7 luglio 2017 |Da definire |
|~~WA-GUEST-OS-3.46_201612-01~~ |10 gennaio 2017 |5 giugno 2017 |Da definire |
|~~WA-GUEST-OS-3.45_201611-01~~ |14 dicembre 2016 |9 maggio 2017 |Da definire |
|~~WA-GUEST-OS-3.44_201610-02~~ |16 novembre 2016 |1 maggio 2017 |Da definire |
|~~WA-GUEST-OS-3.43_201609-01~~ |13 ottobre 2016 |14 gennaio 2017 |Da definire |
|~~WA-GUEST-OS-3.42_201608-01~~ |13 settembre 2016 |16 dicembre 2016 |Da definire |
|~~WA-GUEST-OS-3.41_201607-01~~ |8 agosto 2016 |13 novembre 2016 |Da definire |


## <a name="family-2-releases"></a>Versioni della famiglia 2
**Windows Server 2008 R2 SP1**

Supporta .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> La date contrassegnate da * sono soggette a modifiche.
>
>

| Stringa di configurazione | Data di rilascio | Data di disabilitazione | Data di scadenza |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.66_201709-01 |6 ottobre 2017 |Post 2.68 |Da definire |
| WA-GUEST-OS-2.65_201708-01 |24 agosto 2017 |Dopo la versione 2.67 |Da definire |
| WA-GUEST-OS-2.64_201707-01 |3 agosto 2017 |Post 2.66 |Da definire |
|~~WA-GUEST-OS-2.63_201706-01~~ |7 luglio 2017 |6 ottobre 2017 |Da definire |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 giugno 2017 |24 agosto 2017 |Da definire |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 maggio 2017 |3 agosto 2017 |Da definire |
|~~WA-GUEST-OS-2.60_201703-01~~ |10 aprile 2017 |7 luglio 2017 |Da definire |
|~~WA-GUEST-OS-2.59_201701-01~~ |10 gennaio 2017 |5 giugno 2017 |Da definire |
|~~WA-GUEST-OS-2.58_201612-01~~ |10 gennaio 2017 |9 maggio 2017|Da definire |
|~~WA-GUEST-OS-2.57_201611-01~~ |14 dicembre 2016 |10 aprile 2017 |Da definire |
|~~WA-GUEST-OS-2.56_201610-02~~ |16 novembre 2016 |10 febbraio 2017 |Da definire |
|~~WA-GUEST-OS-2.55_201609-01~~ |13 ottobre 2016 |14 gennaio 2017 |Da definire |
|~~WA-GUEST-OS-2.54_201608-01~~ |13 settembre 2016 |16 dicembre 2016 |Da definire |
|~~WA-GUEST-OS-2.53_201607-01~~ |8 agosto 2016 |13 novembre 2016 |Da definire |



## <a name="msrc-patch-updates"></a>Patch di aggiornamento MSRC
L'elenco di patch incluse con il rilascio del sistema operativo guest di ogni mese è disponibile [qui][patches].

## <a name="sdk-support"></a>Supporto SDK
Anche se le [informazioni sul ritiro di Azure SDK][retire policy sdk] indicano che solo le versioni successive alla 2.2 sono supportate, determinate famiglie di sistemi operativi guest consentono di usare versioni precedenti. È sempre necessario usare l'SDK più recente supportato.

| Famiglia del sistema operativo guest | Versioni dell’SDK compatibili |
| --- | --- |
| 5 |Versione 2.9.5.1+ |
| 4 |Versione 2.1+ |
| 3 |Versione 1.8+ |
| 2 |Versione 1.3+ |
| 1 |Versione 1.0+ |

## <a name="guest-os-release-information"></a>Informazioni sui rilasci del sistema operativo guest
Sono tre le date importanti per le versioni dei sistemi operativi guest: la data di **rilascio**, la data di **disabilitazione** e la data di **scadenza**. Un sistema operativo guest è considerato disponibile quando è disponibile sul portale e può essere selezionato come sistema operativo guest di destinazione. Quando un sistema operativo guest raggiunge la data di **disabilitazione**, viene rimosso da Azure. Tuttavia, qualsiasi servizio cloud destinato a tale sistema operativo guest funzionerà normalmente.

La finestra temporale fra la data di **disabilitazione** e la data di **scadenza** offre un margine di tempo che facilita la transizione da un sistema operativo guest a un altro più recente. Se si usa l'opzione *automatico* come sistema operativo guest, la versione usata sarà sempre la più recente e non ci si dovrà preoccupare della scadenza.

Quando si supera la data di **scadenza** , qualsiasi servizio cloud che usa ancora quel sistema operativo guest verrà arrestato, eliminato o costretto all'aggiornamento. Altre informazioni sui criteri di ritiro sono disponibili [qui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Spiegazione delle famiglie e delle versioni del sistema operativo guest
Le famiglie dei sistemi operativi guest sono basate sulle versioni rilasciate di Microsoft Windows Server e il sistema operativo guest è il sistema operativo sottostante in cui viene eseguito Servizi cloud di Azure. A ogni sistema operativo guest sono associati una famiglia, una versione e un numero di rilascio.

* **Guest OS family**  
  Una versione del sistema operativo Windows Server sulla quale è basato un sistema operativo guest. Ad esempio, la *famiglia 3* è basata su Windows Server 2012.
* **Versione del sistema operativo guest**  
  Specifica per l'immagine di una famiglia di sistemi operativi guest e per le patch pertinenti di [Microsoft Security Response Center (MSRC)][msrc] disponibili al momento dell'introduzione della nuova versione del sistema operativo guest. È possibile che non siano incluse tutte le patch.

    I numeri iniziano da 0 e vengono incrementati di 1 a ogni aggiunta di un nuovo set di aggiornamenti. Gli zeri finali vengono visualizzati solo se sono importanti; ad esempio, la versione 2.10 è una versione diversa e successiva rispetto alla versione 2.1.
* **Rilascio del sistema operativo guest**  
  Un nuovo rilascio di una versione del sistema operativo guest. Il nuovo rilascio viene introdotto se durante la fase di test Microsoft rileva problemi che richiedono l'esecuzione di modifiche. L'ultimo rilascio sostituisce sempre quelli precedenti, che siano pubblici o meno. Il portale di Azure consentirà agli utenti di scaricare soltanto l'ultimo rilascio disponibile per una determinata versione. Le distribuzioni in cui si usa una versione precedente in genere non vengono forzate all'aggiornamento in base alla gravità del bug.

Nell'esempio seguente, 2 rappresenta la famiglia, 12 la versione e "rel2" il rilascio.

**Rilascio del sistema operativo guest** - 2.12 rel2

**Stringa di configurazione per questo rilascio** - WA-GUEST-OS-2.12_201208-02

Nella stringa di configurazione per un sistema operativo guest sono contenute queste stesse informazioni, insieme a una data che indica quali patch di MSRC sono state prese in considerazione per il rilascio. In questo esempio, sono state incluse le patch di MSRC prodotte per Windows Server 2008 R2 fino ad agosto 2012. Vengono incluse solo le patch che si applicano specificatamente alla versione di Windows Server. Ad esempio, se una patch MSRC si applica a Microsoft Office, non verrà inclusa poiché il prodotto non fa parte dell'immagine di base di Windows Server.

## <a name="guest-os-system-update-process"></a>Processo di aggiornamento del sistema operativo guest
In questa pagina vengono fornite informazioni sui prossimi rilasci del sistema operativo guest. I clienti hanno indicato di voler sapere quando viene rilasciata una versione poiché i ruoli del servizio cloud vengono riavviati se impostati per l'aggiornamento automatico. Le versioni del sistema operativo guest vengono in genere rilasciate almeno cinque (5) giorni dopo il rilascio dell'aggiornamento di MSRC che avviene il secondo martedì di ogni mese. Le nuove versioni includono tutte le patch importanti di MSRC per ogni famiglia del sistema operativo guest.

Gli aggiornamenti di Microsoft Azure vengono rilasciati costantemente. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da molti fattori, troppo numerosi per essere elencati in questa pagina. Inoltre, Azure viene eseguito su centinaia di migliaia di computer. Ciò significa che è impossibile specificare una data e un'ora esatte del riavvio dei ruoli. Stiamo lavorando a un piano per limitare o temporizzare i riavvii.

Quando viene pubblicato un nuovo rilascio del sistema operativo guest, la propagazione completa in Azure può richiedere tempo. Man mano che i servizi vengono aggiornati al nuovo sistema operativo guest, vengono riavviati rispettando i domini di aggiornamento. Un rilascio sarà innanzitutto fornito ai servizi per cui sono stati impostati gli aggiornamenti automatici. Dopo l'aggiornamento, la nuova versione del sistema operativo guest sarà elencata per il servizio nel portale di Azure. Durante questo periodo possono essere introdotti dei nuovi rilasci. Alcune versioni potrebbero essere distribuite per periodi più lunghi ed è possibile che i riavvii di aggiornamenti automatici non si verifichino per molte settimane dopo la data di rilascio ufficiale. Quando un sistema operativo guest diventa disponibile, è possibile scegliere esplicitamente tale versione dal portale o nel file di configurazione.

Per molte informazioni importanti sui riavvii e sui puntatori ad altre informazioni sui dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post del blog su MSDN relativo ai [riavvii dell'istanza del ruolo dovuti ad aggiornamenti del sistema operativo][restarts].

Se si aggiorna manualmente il sistema operativo guest, vedere [Criteri relativi al supporto e al ritiro del sistema operativo guest di Azure][retirepolicy] per altre informazioni.

## <a name="guest-os-supportability-and-retirement-policy"></a>Criteri relativi al supporto e al ritiro del sistema operativo guest
I criteri relativi al supporto e al ritiro del sistema operativo guest sono descritti [qui][retirepolicy].

[feed RSS di aggiornamento del sistema operativo guest]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml (Feed RSS di aggiornamento del sistema operativo guest)
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure.md
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
[correzioni]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
