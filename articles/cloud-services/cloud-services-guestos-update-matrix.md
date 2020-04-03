---
title: Informazioni sulle versioni più recenti del sistema operativo guest Azure | Documentazione Microsoft
description: Notizie sui rilasci più recenti e sulla compatibilità SDK per il sistema operativo guest di Servizi cloud di Azure.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/2/2020
ms.author: raiye
ms.openlocfilehash: 95c27cd906717d52a232b5ed85eba2b93bc6569d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618284"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK
Fornisce informazioni aggiornate sui rilasci del sistema operativo guest Azure più recente per i servizi cloud. Queste informazioni sono utili per pianificare il percorso di aggiornamento prima che un sistema operativo guest venga disabilitato. Se si configurano i ruoli per l'uso degli aggiornamenti *automatici* del sistema operativo guest come descritto in [Impostazioni di aggiornamento del sistema operativo guest Azure][Azure Guest OS Update Settings], non è indispensabile leggere questa pagina.

> [!IMPORTANT]
> Questa pagina è applicabile ai ruoli Web e ai ruoli di lavoro dei servizi cloud in esecuzione su un sistema operativo guest. **Non si applica** alle macchine virtuali IaaS.
>
>


> [!TIP]
>  Sottoscrivere il [feed RSS di aggiornamento del sistema operativo guest] per ricevere notifiche più tempestive su tutte le modifiche al sistema operativo guest.
>
>

> [!IMPORTANT]
> Solo le due versioni più recenti del sistema operativo Guest saranno supportate e disponibili nel portale di Azure.
>
>

In caso di dubbi su come aggiornare il sistema operativo guest, vedere [queste informazioni][cloud updates].

## <a name="news-updates"></a>Novità e aggiornamenti

###### <a name="april-2-2020"></a>**giovedì 2 aprile 2020**
È stato rilasciato il sistema operativo guest di marzo. 

###### <a name="march-5-2020"></a>**5 marzo 2020**
È stato rilasciato il sistema operativo guest di febbraio. 

###### <a name="january-24-2020"></a>**24 gennaio 2020**
È stato rilasciato il sistema operativo guest di gennaio. 

###### <a name="january-8-2020"></a>**8 gennaio 2020**
È stato rilasciato il sistema operativo guest di dicembre.

###### <a name="december-5-2019"></a>**5 dicembre 2019**
È stato rilasciato il sistema operativo guest di novembre.

###### <a name="november-1-2019"></a>**1 novembre 2019**
È stato rilasciato il sistema operativo guest di ottobre.

###### <a name="october-7-2019"></a>**giovedì 7 ottobre 2019**
È stato rilasciato il sistema operativo guest di settembre.

###### <a name="september-4-2019"></a>**4 settembre 2019**
È stato rilasciato il sistema operativo guest di agosto.

###### <a name="july-26-2019"></a>**26 luglio 2019**
È stato rilasciato il sistema operativo guest di luglio.

###### <a name="july-8-2019"></a>**giovedì 8 luglio 2019**
È stato rilasciato il sistema operativo guest di giugno.

###### <a name="june-6-2019"></a>**giovedì 6 giugno 2019**
È stato rilasciato il sistema operativo guest di maggio.

###### <a name="may-7-2019"></a>**giovedì 7 maggio 2019**
È stato rilasciato il sistema operativo guest di aprile.

###### <a name="march-26-2019"></a>**26 marzo 2019**
È stato rilasciato il sistema operativo guest di marzo.

###### <a name="march-12-2019"></a>**12 marzo 2019**
È stato rilasciato il sistema operativo guest di febbraio.

###### <a name="february-5-2019"></a>**5 febbraio 2019**
È stato rilasciato il sistema operativo guest di gennaio.

###### <a name="january-24-2019"></a>**24 gennaio 2019**
È stata rilasciata la famiglia 6 di sistemi operativi guest (Windows Server 2019).

###### <a name="january-7-2019"></a>**7 gennaio 2019**
È stato rilasciato il sistema operativo guest di dicembre.

###### <a name="december-14-2018"></a>**14 dicembre 2018**
È stato rilasciato il sistema operativo guest di novembre.

###### <a name="november-8-2018"></a>**8 novembre 2018**
È stato rilasciato il sistema operativo guest di ottobre.

###### <a name="october-12-2018"></a>**12 ottobre 2018**
È stato rilasciato il sistema operativo guest di settembre.

## <a name="releases"></a>Rilasci

## <a name="family-6-releases"></a>Versioni della famiglia 6
**Windows Server 2019**

.NET Framework installato: 3.5, 4.7.2

> [!NOTE]
> Windows Azure SDK per .NET - 3.0 può essere scaricato [qui][Windows Azure SDK].
>
>Passaggi dell'installazione:
>1. Disinstallare tutte le versioni precedenti di MicrosoftAzureAuthoringTools.msi
>2. Installare [Azure SDK per .NET - 3.0Install the Azure SDK for .NET - 3.0][Windows Azure SDK]
>3. Riavviare il computer
>4. Creare un nuovo progetto di servizio cloud e aggiungere un singolo ruolo di lavoroCreate a new Cloud Service project and add a single Worker Role
>5. Cambia la famiglia di sistema operativo a 6 e crea un pacchetto
>6. Distribuire il pacchetto in Azure usando il portale di Azure o Visual StudioDeploy the package to Azure using the Azure portal or Visual Studio
>
>La versione guest OS Family 6 impone TLS 1.2 disabilitando in modo esplicito TLS 1.0 e 1.1 e definendo un set specifico di suite di crittografia. Ulteriori [more]informazioni .


| Stringa di configurazione | Data di rilascio | Data di disabilitazione |
| --- | --- | --- |
|  WA-GUEST-OS-6.17_202003-01  |  giovedì 2 aprile 2020  |  Post 6.19  |  
|  WA-GUEST-OS-6.16_202002-01  |  5 marzo 2020  |  Post 6.18  |  
|~~WA-GUEST-OS-6.15_202001-01~~|  24 gennaio 2020  |  giovedì 2 aprile 2020  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 8 gennaio 2020 | 5 marzo 2020 |  
|~~WA-GUEST-OS-6.13_201911-01~~| 5 dicembre 2019 | 24 gennaio 2020 |  
|~~WA-GUEST-OS-6.12_201910-01~~| 1 novembre 2019 | 8 gennaio 2020 |  
|~~WA-GUEST-OS-6.11_201909-01~~| giovedì 7 ottobre 2019 | 5 dicembre 2019 |  
|~~WA-GUEST-OS-6.10_201908-01~~| giovedì 4 agosto 2019 | 1 novembre 2019  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26 luglio 2019 | giovedì 7 ottobre 2019 |
|~~WA-GUEST-OS-6.8_201906-01~~|giovedì 8 luglio 2019 |giovedì 4 agosto 2019 |
|~~WA-GUEST-OS-6.7_201905-01~~ |giovedì 6 giugno 2019 |26 luglio 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |giovedì 7 maggio 2019 |giovedì 8 luglio 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 marzo 2019 |giovedì 6 giugno 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 marzo 2019 |giovedì 7 maggio 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 febbraio 2019 |26 marzo 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 gennaio 2019 |12 marzo 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 gennaio 2019 |5 febbraio 2019 |

## <a name="family-5-releases"></a>Versioni della famiglia 5
**Windows Server 2016**

.NET Framework installato: 3.5, 4.6

> [!NOTE]
> La password di RDP per la famiglia di sistemi operativi 5 deve contenere almeno 10 caratteri.
>


| Stringa di configurazione | Data di rilascio | Data di disabilitazione |
| --- | --- | --- |
|  WA-GUEST-OS-5.41_202003-01  |  giovedì 2 aprile 2020  |  Post 5.43  |  
|  WA-GUEST-OS-5.40_202002-01  |  5 marzo 2020  |  Post 5.42  |  
|~~WA-GUEST-OS-5.39_202001-01~~|  24 gennaio 2020  |  giovedì 2 aprile 2020  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 8 gennaio 2020 | 5 marzo 2020 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5 dicembre 2019 | 24 gennaio 2020 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1 novembre 2019 | 8 gennaio 2020 |  
|~~WA-GUEST-OS-5.35_201909-01~~| giovedì 7 ottobre 2019 | 5 dicembre 2019 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  giovedì 4 agosto 2019  | 1 novembre 2019 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26 luglio 2019 | giovedì 7 ottobre 2019 |  
|~~WA-GUEST-OS-5.32_201906-01~~|giovedì 8 luglio 2019 |giovedì 4 agosto 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |giovedì 6 giugno 2019 |26 luglio 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |giovedì 7 maggio 2019 |giovedì 8 luglio 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 marzo 2019 |giovedì 6 giugno 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 marzo 2019 |giovedì 7 maggio 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 febbraio 2019 |26 marzo 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 gennaio 2019 |12 marzo 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 dicembre 2018 |5 febbraio 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 novembre 2018 |7 gennaio 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 ottobre 2018 |14 dicembre 2018 |

## <a name="family-4-releases"></a>Versioni della famiglia 4
**Windows Server 2012 R2**

.NET Framework installato: 3.5, 4.5.1, 4.5.2

| Stringa di configurazione | Data di rilascio | Data di disabilitazione |
| --- | --- | --- |
|  WA-GUEST-OS-4.76_202003-01  |  giovedì 2 aprile 2020  |  Post 4.78  |  
|  WA-GUEST-OS-4.75_202002-01  |  5 marzo 2020  |  Post 4.77  |  
|~~WA-GUEST-OS-4.74_202001-01~~|  24 gennaio 2020  |  giovedì 2 aprile 2020  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 8 gennaio 2020 | 5 marzo 2020 |  
|~~WA-GUEST-OS-4.72_201911-01~~| 5 dicembre 2019 | 24 gennaio 2020 |  
|~~WA-GUEST-OS-4.71_201910-01~~| 1 novembre 2019 | 8 gennaio 2020 |  
|~~WA-GUEST-OS-4.70_201909-01~~| giovedì 7 ottobre 2019 | 5 dicembre 2019 |  
|~~WA-GUEST-OS-4.69_201908-01~~| giovedì 4 agosto 2019 | 1 novembre 2019 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26 luglio 2019  | giovedì 7 ottobre 2019 |
|~~WA-GUEST-OS-4.67_201906-01~~| giovedì 8 luglio 2019 |giovedì 4 agosto 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |giovedì 6 giugno 2019 |26 luglio 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |giovedì 7 maggio 2019 |giovedì 8 luglio 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 marzo 2019 |giovedì 6 giugno 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 marzo 2019 |giovedì 7 maggio 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 febbraio 2019 |26 marzo 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 gennaio 2019 |12 marzo 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 dicembre 2018 |5 febbraio 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 novembre 2018 |7 gennaio 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 ottobre 2018 |14 dicembre 2018 |

## <a name="family-3-releases"></a>Versioni della famiglia 3
**Windows Server 2012 (informazioni in due)**

Versione .NET Framework installata: 3.5, 4.5

| Stringa di configurazione | Data di rilascio | Data di disabilitazione |
| --- | --- | --- |
|  WA-GUEST-OS-3.83_202003-01  |  giovedì 2 aprile 2020  |  Post 3.85  |  
|  WA-GUEST-OS-3.82_202002-01  |  5 marzo 2020  |  Post 3.84  |  
|~~WA-GUEST-OS-3.81_202001-01~~|  24 gennaio 2020  |  giovedì 2 aprile 2020  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 8 gennaio 2020 | 5 marzo 2020 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5 dicembre 2019 | 24 gennaio 2020 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1 novembre 2019 | 8 gennaio 2020 |  
|~~WA-GUEST-OS-3.77_201909-01~~| giovedì 7 ottobre 2019 | 5 dicembre 2019 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  giovedì 4 agosto 2019  |  1 novembre 2019  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26 luglio 2019 | giovedì 7 ottobre 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| giovedì 8 luglio 2019 |giovedì 4 agosto 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |giovedì 6 giugno 2019 |26 luglio 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |giovedì 7 maggio 2019 |giovedì 8 luglio 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 marzo 2019 |giovedì 6 giugno 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 marzo 2019 |giovedì 7 maggio 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 febbraio 2019 |26 marzo 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 gennaio 2019 |12 marzo 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 dicembre 2018 |5 febbraio 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 novembre 2018 |7 gennaio 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 ottobre 2018 |14 dicembre 2018 |

## <a name="family-2-releases"></a>Versioni della famiglia 2
**Windows Server 2008 R2 SP1**

.NET Framework installato: 3.5 (include 2.0 e 3.0), 4.5

| Stringa di configurazione | Data di rilascio | Data di disabilitazione |
| --- | --- | --- |
|  WA-GUEST-OS-2.96_202003-01  |  giovedì 2 aprile 2020  |  Post 2.98  |  
|  WA-GUEST-OS-2.95_202002-01  |  5 marzo 2020  |  Post 2.97  |  
|~~WA-GUEST-OS-2.94_202001-01~~|  24 gennaio 2020  |  giovedì 2 aprile 2020  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 8 gennaio 2020 | 5 marzo 2020 |  
|~~WA-GUEST-OS-2.92_201911-01~~| 5 dicembre 2019 | 24 gennaio 2020 |  
|~~WA-GUEST-OS-2.91_201910-01~~| 1 novembre 2019 | 8 gennaio 2020 |  
|~~WA-GUEST-OS-2.90_201909-01~~| giovedì 7 ottobre 2019 | 5 dicembre 2019 |  
|~~WA-GUEST-OS-2.89_201908-01~~| giovedì 4 agosto 2019 | 1 novembre 2019 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26 luglio 2019 | giovedì 7 ottobre 2019 |
|~~WA-GUEST-OS-2.87_201906-01~~|giovedì 8 luglio 2019 | giovedì 4 agosto 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |giovedì 6 giugno 2019 |26 luglio 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |giovedì 7 maggio 2019 |giovedì 8 luglio 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 marzo 2019 |giovedì 6 giugno 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 marzo 2019 |giovedì 7 maggio 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 febbraio 2019 |26 marzo 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 gennaio 2019 |12 marzo 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 dicembre 2018 |5 febbraio 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 novembre 2018 |7 gennaio 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 ottobre 2018 |14 dicembre 2018 |

## <a name="msrc-patch-updates"></a>Patch di aggiornamento MSRC
L'elenco di patch incluse con il rilascio del sistema operativo guest di ogni mese è disponibile [qui][patches].

## <a name="sdk-support"></a>Supporto SDK
Anche se le [informazioni sul ritiro di Azure SDK][retire policy sdk] indicano che solo le versioni successive alla 2.2 sono supportate, determinate famiglie di sistemi operativi guest consentono di usare versioni precedenti. È sempre necessario usare l'SDK più recente supportato.

| Famiglia del sistema operativo guest | Versioni SDK compatibili |
| --- | --- |
| 6 |Versione 2.9.6+ |
| 5 |Versione 2.9.5.1+ |
| 4 |Versione 2.1+ |
| 3 |Versione 1.8+ |
| 2 |Versione 1.3+ |
| 1 |Versione 1.0+ |

## <a name="guest-os-release-information"></a>Informazioni sulle versioni dei sistemi operativi guest
Sono tre le date importanti per le versioni dei sistemi operativi guest: la data di **rilascio**, la data di **disabilitazione** e la data di **scadenza**. Un sistema operativo guest è considerato disponibile quando è disponibile sul portale e può essere selezionato come sistema operativo guest di destinazione. Quando un sistema operativo guest raggiunge la data di **disabilitazione**, viene rimosso da Azure. Tuttavia, qualsiasi servizio cloud destinato a tale sistema operativo guest funzionerà normalmente.

La finestra temporale fra la data di **disabilitazione** e la data di **scadenza** offre un margine di tempo che facilita la transizione da un sistema operativo guest a un altro più recente. Se si usa l'opzione *automatico* come sistema operativo guest, la versione usata sarà sempre la più recente e non ci si dovrà preoccupare della scadenza.

Quando si supera la data di **scadenza** , qualsiasi servizio cloud che usa ancora quel sistema operativo guest verrà arrestato, eliminato o costretto all'aggiornamento. Altre informazioni sui criteri di ritiro sono disponibili [qui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Spiegazione delle versioni delle famiglie di sistemi operativi guest
Le famiglie dei sistemi operativi guest sono basate sulle versioni rilasciate di Microsoft Windows Server e il sistema operativo guest è il sistema operativo sottostante in cui viene eseguito Servizi cloud di Azure. A ogni sistema operativo guest sono associati una famiglia, una versione e un numero di rilascio.

* **Famiglia di oS guest**  
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
In questa pagina vengono fornite informazioni sui prossimi rilasci del sistema operativo guest. I clienti hanno indicato di voler sapere quando viene rilasciata una versione poiché i ruoli del servizio cloud vengono riavviati se impostati per l'aggiornamento automatico. Le versioni del sistema operativo guest vengono in genere rilasciate 2-3 settimane dopo il rilascio dell'aggiornamento di MSRC che avviene il secondo martedì di ogni mese. Le nuove versioni includono tutte le patch importanti di MSRC per ogni famiglia del sistema operativo guest.

Gli aggiornamenti di Microsoft Azure vengono rilasciati costantemente. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da molti fattori, troppo numerosi per essere elencati in questa pagina. Inoltre, Azure viene eseguito su centinaia di migliaia di computer. Ciò significa che è impossibile specificare una data e un'ora esatte del riavvio dei ruoli. Stiamo lavorando a un piano per limitare o temporizzare i riavvii.

Quando viene pubblicato un nuovo rilascio del sistema operativo guest, la propagazione completa in Azure può richiedere tempo. Man mano che i servizi vengono aggiornati al nuovo sistema operativo guest, vengono riavviati rispettando i domini di aggiornamento. Un rilascio sarà innanzitutto fornito ai servizi per cui sono stati impostati gli aggiornamenti automatici. Dopo l'aggiornamento, la nuova versione del sistema operativo guest sarà elencata per il servizio nel portale di Azure. Durante questo periodo possono essere introdotti dei nuovi rilasci. Alcune versioni potrebbero essere distribuite per periodi più lunghi ed è possibile che i riavvii di aggiornamenti automatici non si verifichino per molte settimane dopo la data di rilascio ufficiale. Quando un sistema operativo guest diventa disponibile, è possibile scegliere esplicitamente tale versione dal portale o nel file di configurazione.

Per molte informazioni importanti sui riavvii e sui puntatori ad altre informazioni sui dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post del blog su MSDN relativo ai [riavvii dell'istanza del ruolo dovuti ad aggiornamenti del sistema operativo][restarts].

Se si aggiorna manualmente il sistema operativo guest, vedere [Criteri relativi al supporto e al ritiro del sistema operativo guest di Azure][retirepolicy] per altre informazioni.

## <a name="guest-os-supportability-and-retirement-policy"></a>Criteri relativi al supporto e al ritiro del sistema operativo guest
I criteri relativi al supporto e al ritiro del sistema operativo guest sono descritti [qui][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Guest OS Update RSS Feed]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml (Feed RSS di aggiornamento del sistema operativo guest)
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Più]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
