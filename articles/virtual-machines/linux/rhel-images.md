---
title: Immagini Red Hat Enterprise Linux in Azure | Microsoft Docs
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 7d34e480dd3cf90f1948e83ea1d18c04f1dcdce2
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854434"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Immagini Red Hat Enterprise Linux in Azure
Questo articolo illustra le immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure Marketplace con i criteri per la denominazione e conservazione.

Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).

>[!Important]
> Le immagini RHEL attualmente disponibili in Azure Marketplace supportano i modelli di licenza BYOS (Bring-Your-Own-Subscription) o PAYG (Pay-As-You-Go, ovvero pagamento in base al consumo). Il [Vantaggio Azure Hybrid Use](../windows/hybrid-use-benefit-licensing.md) e il passaggio dinamico da BYOS a PAYG e viceversa non sono supportati. Il passaggio da una modalità di licenza all'altra richiede la ridistribuzione della macchina virtuale dall'immagine corrispondente.

>[!Note]
> Per qualsiasi problema correlato alle immagini RHEL nella raccolta di Azure Marketplace, inviare un ticket di supporto a Microsoft.

## <a name="images-available-in-the-ui"></a>Immagini disponibili nell'interfaccia utente
Quando si cerca "Red Hat" nel Marketplace o quando si crea una risorsa nell'interfaccia utente del portale di Azure, verranno visualizzati un subset di immagini RHEL disponibili e dei prodotti Red Hat correlati. È sempre possibile ottenere il set completo di immagini di VM disponibili usando l'interfaccia della riga di comando/PowerShell/API di Azure.

Per visualizzare il set completo di immagini RedHat disponibili in Azure, eseguire il comando seguente

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenzione di denominazione
Le immagini di VM in Azure sono organizzate per editore, offerta, SKU e versione. La combinazione di editore:offerta:SKU:versione è l'URN dell'immagine e identifica in modo univoco l'immagine da utilizzare.

Ad esempio, `RedHat:RHEL:7-RAW:7.6.2018103108` fa riferimento a un'immagine RHEL 7.6 con partizione raw compilata il 31 ottobre 2018.

Di seguito è illustrato un esempio di come creare una VM RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Il percorso del collegamento "latest"
L'API REST di Azure consente l'uso del percorso del collegamento "latest" per la versione in alternativa alla versione specifica. L'uso di "latest" eseguirà il provisioning dell'immagine più recente disponibile per l'editore, l'offerta e lo SKU specificati.

Ad esempio, `RedHat:RHEL:7-RAW:latest` fa riferimento all'immagine con partizione raw della famiglia RHEL 7 più recente disponibile.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> In generale, il confronto delle versioni per determinare la versione più recente segue le regole del [metodo CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenzione di denominazione attuale
Tutte le immagini RHEL attualmente pubblicate utilizzano il modello con pagamento in base al consumo e sono connesse all'[infrastruttura di aggiornamento per Red Hat (RHUI) in Azure](https://aka.ms/rhui-update). È stata adottata una nuova convenzione di denominazione per le immagini della famiglia RHEL 7 in cui lo schema di partizionamento del disco (RAW, LVM) viene specificato nello SKU anziché nella versione. La versione dell'immagine RHEL conterrà 7-RAW o 7-LVM. La denominazione della famiglia RHEL 6 non è stata attualmente modificata.

In questa convenzione di denominazione saranno disponibili 2 tipi di SKU di immagini RHEL 7: SKU che elencano la versione secondaria e SKU che non lo sono. Se si vuole usare uno SKU 7-RAW o 7 LVM, è possibile specificare la versione secondaria RHEL che si vuole distribuire nella versione. Se si sceglie la versione "più recente", verrà effettuato il provisioning della versione secondaria più recente di RHEL.

>[!NOTE]
> Nel set di immagini RHEL per SAP, la versione RHEL rimane fissa. Di conseguenza, la convenzione di denominazione include una versione particolare nello SKU.

>[!NOTE]
> Il set di immagini RHEL 6 non è stato spostato alla nuova convenzione di denominazione.

## <a name="extended-update-support-eus"></a>Supporto degli aggiornamenti estesi (EUS)
A partire dal 2019 aprile, sono disponibili immagini RHEL associate ai repository di supporto per aggiornamenti estesi (EUS) per impostazione predefinita. Altre informazioni su RHEL EUS sono disponibili nella [documentazione di Red Hat](https://access.redhat.com/articles/rhel-eus).

Per istruzioni su come passare la macchina virtuale a EUS e per altre informazioni sulle date di fine del supporto EUS, vedere [qui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS non è supportato in extra RHEL. Ciò significa che se si installa un pacchetto che in genere è disponibile dal canale extra RHEL, non sarà possibile eseguire questa operazione in EUS. Il ciclo di vita del prodotto Red Hat extra è descritto [qui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Per i clienti che vogliono usare le immagini EUS:
I clienti che vogliono usare le immagini collegate ai repository EUS devono usare l'immagine RHEL contenente un numero di versione secondario RHEL nello SKU. Queste immagini saranno partizionate in RAW (ovvero non LVM).

Ad esempio, è possibile visualizzare le 2 immagini RHEL 7,4 seguenti:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
In questo caso, `RedHat:RHEL:7.4:7.4.2019041718` verrà allegato ai repository EUS per impostazione predefinita e `RedHat:RHEL:7-RAW:7.4.2018010506` verrà collegato ai repository non EUS per impostazione predefinita.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Per i clienti che non vogliono usare immagini EUS:
Se non si vuole usare un'immagine connessa a EUS per impostazione predefinita, effettuare la distribuzione usando un'immagine che non contiene un numero di versione secondario nello SKU.

#### <a name="rhel-images-with-eus"></a>Immagini RHEL con EUS
La tabella seguente si applica per le immagini RHEL che contengono una versione secondaria nello SKU.

>[!NOTE]
> Al momento della stesura di questo articolo, solo RHEL 7,4 e versioni secondarie successive hanno il supporto EUS. EUS non è più supportato per RHEL < = 7,3.

Versione secondaria |Esempio di immagine EUS              |Stato EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat: RHEL: 7.4:7.4.2019041718 | Le immagini pubblicate il 2019 aprile e successive saranno EUS per impostazione predefinita|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Le immagini pubblicate il 2019 giugno e versioni successive saranno EUS per impostazione predefinita |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Le immagini pubblicate il 2019 e versioni successive saranno EUS per impostazione predefinita  |
RHEL 8,0      |N/D                            | Attualmente non sono disponibili immagini EUS                 |


## <a name="list-of-rhel-images-available"></a>Elenco di immagini RHEL disponibili
Le offerte seguenti sono SKU attualmente disponibili per l'uso generale:

Offerta| SKU | Partizionamento | Provisioning | Note
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Agente Linux | Famiglia di immagini RHEL 7. <br> Per impostazione predefinita, non è collegato ai repository EUS.
|             | 7-LVM    | LVM    | Agente Linux | Famiglia di immagini RHEL 7. <br> Per impostazione predefinita, non è collegato ai repository EUS.
|             | 7-RAW-CI | RAW-CI | cloud-init  | Famiglia di immagini RHEL 7. <br> Per impostazione predefinita, non è collegato ai repository EUS.
|             | 6.7      | RAW    | Agente Linux | Immagini RHEL 6.7, convenzione di denominazione precedente
|             | 6.8      | RAW    | Agente Linux | Come sopra per RHEL 6.8
|             | 6.9      | RAW    | Agente Linux | Come sopra per RHEL 6.9
|             | 6.10     | RAW    | Agente Linux | Come sopra per RHEL 6.10
|             | 7.2      | RAW    | Agente Linux | Come sopra per RHEL 7.2
|             | 7.3      | RAW    | Agente Linux | Come sopra per RHEL 7.3
|             | 7.4      | RAW    | Agente Linux | Come descritto in precedenza per RHEL 7,4. <br> Allegato ai repository EUS per impostazione predefinita a partire dal 2019 aprile
|             | 7.5      | RAW    | Agente Linux | Come descritto in precedenza per RHEL 7,5. <br> Allegato ai repository EUS per impostazione predefinita a partire dal 2019 giugno
|             | 7.6      | RAW    | Agente Linux | Come descritto in precedenza per RHEL 7,6. <br> Allegato ai repository EUS per impostazione predefinita a partire dal 2019 maggio
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 per SAP HANA e app aziendali
|             | 7.5      | LVM    | Agente Linux | RHEL 7.5 per SAP HANA e app aziendali
RHEL-SAP-HANA | 6.7      | RAW    | Agente Linux | RHEL 6.7 per SAP HANA
|             | 7.2      | LVM    | Agente Linux | RHEL 7.2 per SAP HANA
|             | 7.3      | LVM    | Agente Linux | RHEL 7.3 per SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Agente Linux | RHEL 6.8 per Business Applications SAP
|             | 7.3      | LVM    | Agente Linux | RHEL 7.3 per Business Applications SAP
RHEL-HA       | 7.4      | RAW    | Agente Linux | RHEL 7,4 con componente aggiuntivo a disponibilità elevata
|             | 7.5      | RAW    | Agente Linux | RHEL 7,5 con componente aggiuntivo a disponibilità elevata
|             | 7.6      | RAW    | Agente Linux | RHEL 7,6 con componente aggiuntivo a disponibilità elevata
RHEL-SAP-HA   | 7.4      | RAW    | Agente Linux | RHEL 7,4 per SAP con componente aggiuntivo a disponibilità elevata
|             | 7.5      | RAW    | Agente Linux | RHEL 7,5 per SAP con componente aggiuntivo a disponibilità elevata
|             | 7.6      | RAW    | Agente Linux | RHEL 7,6 per SAP con componente aggiuntivo a disponibilità elevata

### <a name="old-naming-convention"></a>Convenzione di denominazione precedente
La famiglia di immagini RHEL 7 e la famiglia di immagini RHEL 6 utilizzavano versioni specifiche nei relativi SKU fino alla modifica della convenzione di denominazione descritta in precedenza.

Saranno presenti SKU numerici nell'elenco completo delle immagini. Microsoft e Red Hat creeranno nuovi SKU numerici quando viene resa disponibile una nuova versione secondaria.

### <a name="other-available-offers-and-skus"></a>Altre offerte e altri SKU disponibili
L'elenco completo delle offerte e degli SKU disponibili può includere immagini aggiuntive oltre a quelle elencate nella tabella precedente, ad esempio, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Queste offerte possono essere usate per fornire il supporto di soluzioni di marketplace specifiche oppure possono essere pubblicate per anteprime e a scopo di test. Possono essere modificate o rimosse in qualsiasi momento senza preavviso. Non utilizzarle a meno che la loro presenza non sia stata documentata pubblicamente da Microsoft o Red Hat.

## <a name="publishing-policy"></a>Criteri di pubblicazione
Microsoft e Red Hat aggiornano le immagini man mano che vengono rilasciate nuove versioni secondarie, in base alle esigenze per soddisfare CVE specifiche o per modifiche o aggiornamenti occasionali della configurazione. Ci impegniamo a fornire le immagini aggiornate appena possibile, entro tre giorni lavorativi dopo un rilascio o la disponibilità di una correzione CVE.

Aggiorniamo la versione secondaria corrente solo in una famiglia di immagini specificata. Con il rilascio di una versione secondaria più recente, interrompiamo l'aggiornamento della versione secondaria precedente. Ad esempio, con la versione di RHEL 7.6, le immagini RHEL 7.5 non verranno più aggiornate.

>[!NOTE]
> Le VM di Azure attive il cui provisioning è stato eseguito da immagini RHEL con pagamento in base al consumo sono connesse all'infrastruttura RHUI di Azure e possono ricevere aggiornamenti e correzioni non appena vengono rilasciati da Red Hat e replicati nell'infrastruttura RHUI di Azure (in genere in meno di 24 ore a seguito del rilascio ufficiale da parte di Red Hat). Queste VM non richiedono una nuova immagine pubblicata per ottenere gli aggiornamenti e i clienti hanno il controllo completo sul momento in cui avviare l'aggiornamento.

## <a name="image-retention-policy"></a>Criteri di conservazione delle immagini
I criteri attuali prevedono la conservazione di tutte le immagini pubblicate in precedenza. Ci riserviamo il diritto di rimuovere le immagini che potrebbero causare problemi di qulasiasi tipo. Ad esempio, le immagini con configurazioni non corrette a causa di aggiornamenti successivi della piattaforma o dei componenti potrebbero essere rimosse. Le immagini che possono essere rimosse seguiranno i criteri di Marketplace correnti per fornire notifiche fino a 30 giorni prima della rimozione dell'immagine.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'infrastruttura di aggiornamento per Azure Red Hat [in questo articolo](https://aka.ms/rhui-update).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata) (Ciclo di vita di Red Hat Enterprise Linux).
