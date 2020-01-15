---
title: Immagini Red Hat Enterprise Linux in Azure | Microsoft Docs
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 211ac68fd10cd745faf68a5efae7392345008d7b
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941454"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Cenni preliminari sulle immagini Red Hat Enterprise Linux
Questo articolo illustra le immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure Marketplace con i criteri per la denominazione e conservazione.

Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata). Per informazioni dettagliate sui prezzi, visitare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Le immagini RHEL attualmente disponibili in Azure Marketplace supportano i modelli di licenza BYOS (Bring-Your-Own-Subscription) o PAYG (Pay-As-You-Go, ovvero pagamento in base al consumo). Il [Vantaggio Azure Hybrid Use](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) e il passaggio dinamico da BYOS a PAYG e viceversa non sono supportati. Il passaggio da una modalità di licenza all'altra richiede la ridistribuzione della macchina virtuale dall'immagine corrispondente.

>[!NOTE]
> Per eventuali problemi relativi alle immagini RHEL in Azure Marketplace, inviare un ticket di supporto con Microsoft.

## <a name="images-available-in-azure"></a>Immagini disponibili in Azure
Quando si cerca "Red Hat" nel Marketplace o quando si crea una risorsa in portale di Azure interfaccia utente, verrà visualizzato solo un subset di tutte le immagini RHEL disponibili. È sempre possibile ottenere il set completo di immagini di VM disponibili usando l'interfaccia della riga di comando/PowerShell/API di Azure.

Per visualizzare il set completo di immagini RedHat disponibili in Azure, eseguire il comando seguente

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenzione di denominazione
Le immagini di VM in Azure sono organizzate per editore, offerta, SKU e versione. La combinazione di editore:offerta:SKU:versione è l'URN dell'immagine e identifica in modo univoco l'immagine da utilizzare.

Ad esempio, `RedHat:RHEL:7-LVM:7.6.2018103108` fa riferimento a un'immagine RHEL 7,6 LVM con partizionamento creata il 31 ottobre 2018.

Di seguito è illustrato un esempio di come creare una VM RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Il percorso del collegamento "latest"
L'API REST di Azure consente di usare il moniker "più recente" per la versione anziché la versione specifica. L'uso di "latest" eseguirà il provisioning dell'immagine più recente disponibile per l'editore, l'offerta e lo SKU specificati.

Ad esempio, `RedHat:RHEL:7-LVM:latest` si riferisce alla versione più recente dell'immagine partizionata LVM 7, disponibile.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> In generale, il confronto delle versioni per determinare la versione più recente segue le regole del [metodo CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="rhel-6-image-types"></a>Tipi di immagine RHEL 6
Per le immagini RHEL 6. x, i tipi di immagine sono i seguenti:

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 6,9) | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 6.9.2018010506) | Tutte le immagini RHEL 6. x standard seguono questa convenzione
|RedHat | RHEL-BYOS | RHEL-raw69 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 6.9.20181023) | Questa immagine è un'immagine RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 6.8.2017053118) | Si tratta di un'immagine RHEL 6,8 per le applicazioni SAP. È autorizzato ad accedere ai repository di applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 6.7.2017053121) | Si tratta di un RHEL 6,7 per SAP HANA immagine. È autorizzato ad accedere ai repository SAP HANA e ai repository RHEL di base.

### <a name="rhel-7-image-types"></a>Tipi di immagine RHEL 7
Per le immagini RHEL 7. x, sono disponibili diversi tipi di immagine. La tabella seguente illustra i diversi set di immagini disponibili. È possibile visualizzare un elenco completo con il comando AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Se non diversamente specificato, tutte le immagini sono partizionate in LVM e si connetteranno a normali repository RHEL (ovvero non EUS, non E4S). In futuro, si passa alla pubblicazione solo di immagini con partizionamento LVM, ma si è pronti a inviare commenti e suggerimenti su questa decisione. Informazioni dettagliate sul supporto degli aggiornamenti estesi e sui servizi di aggiornamento per SAP sono disponibili nella [pagina Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata).

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 7,6) | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019102813) | Le immagini pubblicate prima del 2019 aprile verranno collegate ai repository RHEL standard. Le immagini pubblicate dopo il 2019 aprile verranno collegate ai repository del supporto per gli aggiornamenti estesi di Red Hat (EUS) per consentire il blocco della versione di una specifica versione secondaria. I clienti che desiderano repository regolari devono usare le immagini che contengono 7-LVM o 7-RAW nel valore SKU (dettagli più avanti). RHEL 7,7 e le immagini successive saranno partizionate con LVM. Tutte le altre immagini in questa categoria sono partizionate in RAW.
|RedHat | RHEL | 7-RAW | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019102813) | Queste immagini sono partizionate in RAW, ovvero non sono stati aggiunti volumi logici.
|RedHat | RHEL | 7-RAW-CI | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019072418) | Queste immagini sono partizionate in RAW, ovvero non è stato aggiunto alcun volume logico, e utilizzeranno cloud-init per il provisioning.
|RedHat | RHEL | 7-LVM | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019062414) | Queste immagini sono partizionate con LVM.
|RedHat | RHEL-BYOS | RHEL-{LVM, RAW} | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.7.20190819) | Queste immagini sono immagini RHEL 7 BYOS. Non sono collegati ad alcun repository e non addebitano la tariffa Premium RHEL. Se si è interessati alle immagini RHEL BYOS, [richiedere l'accesso](https://aka.ms/rhel-byos). I valori dello SKU terminano con la versione secondaria e indicano se l'immagine è RAW o LVM partizionata. Ad esempio, un valore SKU di RHEL-lvm77 indica un'immagine RHEL 7,7 partizionata LVM.
|RedHat | RHEL | RHEL-SAP | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019071300) | Queste immagini sono RHEL per le immagini SAP. Hanno diritto ad accedere ai repository di SAP HANA e applicazioni, nonché ai repository E4S di RHEL. La fatturazione include RHEL Premium e SAP Premium oltre alla tariffa di calcolo di base.
|RedHat | RHEL | RHEL-SAP-HA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019062320) | Queste immagini sono RHEL per SAP con la disponibilità elevata e le immagini dei servizi di aggiornamento. Hanno diritto ad accedere ai repository di SAP HANA e applicazioni e ai repository a disponibilità elevata, nonché ai repository E4S RHEL. Per la fatturazione sono inclusi RHEL Premium, SAP Premium e HA Premium oltre alla tariffa di calcolo di base.
|RedHat | RHEL | RHEL-HA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.6.2019062019) | Si tratta di immagini RHEL che hanno anche il diritto di accedere al componente aggiuntivo di disponibilità elevata. L'addebito è leggermente più alto rispetto a RHEL e alla tariffa di calcolo di base a causa del componente aggiuntivo a disponibilità elevata Premium.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.3.2017053118) | Queste immagini non sono aggiornate perché le applicazioni SAP e i repository di SAP HANA sono stati combinati nei repository SAP. Si tratta di RHEL per le immagini di applicazioni SAP. Hanno diritto ad accedere ai repository di applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 7.3.2018051421) | Queste immagini non sono aggiornate perché le applicazioni SAP e i repository di SAP HANA sono stati combinati nei repository SAP. Si tratta di immagini RHEL for SAP HANA. Hanno diritto ad accedere ai repository SAP HANA e ai repository RHEL di base.

### <a name="rhel-8-image-types"></a>Tipi di immagine RHEL 8
Di seguito sono riportati i dettagli relativi ai tipi di immagine RHEL 8.

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 8.0.20191023) | Queste immagini sono immagini RHEL 8,0 LVM-partizionate connesse ai repository standard di Red Hat.
|RedHat | RHEL | 8-Gen2 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio 8.0.20191024) | Queste immagini sono immagini partizionate di Hyper-V di generazione 2 RHEL 8,0 che sono connesse ai repository standard di Red Hat. Altre informazioni sulle macchine virtuali di seconda generazione in Azure sono disponibili [qui](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="extended-update-support-eus"></a>Supporto degli aggiornamenti estesi (EUS)
A partire dal 2019 aprile, sono disponibili immagini RHEL associate ai repository di supporto per aggiornamenti estesi (EUS) per impostazione predefinita. Altre informazioni su RHEL EUS sono disponibili nella [documentazione di Red Hat](https://access.redhat.com/articles/rhel-eus).

Il trasferimento a repository EUS è possibile ed è supportato. Per istruzioni su come passare la macchina virtuale a EUS e per altre informazioni sulle date di fine del supporto EUS, vedere [qui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS non è supportato in extra RHEL. Ciò significa che se si installa un pacchetto che in genere è disponibile dal canale extra RHEL, non sarà possibile eseguire questa operazione in EUS. Il ciclo di vita del prodotto Red Hat extra è descritto [qui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="differentiating-between-regular-and-eus-images"></a>Differenziazione tra immagini regolari e EUS.
I clienti che vogliono usare le immagini collegate ai repository EUS devono usare l'immagine RHEL contenente un numero di versione secondario RHEL nello SKU.

Ad esempio, è possibile visualizzare le due immagini RHEL 7,4 seguenti:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
In questo caso, `RedHat:RHEL:7.6:7.6.2019102813` verrà allegato ai repository EUS per impostazione predefinita (valore SKU di 7,4) e `RedHat:RHEL:7-LVM:7.6.2019062414` verranno collegati a repository non EUS per impostazione predefinita (valore SKU di 7-LVM).

Se si vuole usare repository regolari (non-EUS), eseguire la distribuzione usando un'immagine che non contiene un numero di versione secondario nello SKU.

#### <a name="rhel-images-with-eus"></a>Immagini RHEL con EUS
La tabella seguente si applica per le immagini RHEL connesse ai repository EUS.

>[!NOTE]
> Al momento della stesura di questo articolo, solo RHEL 7,4 e versioni secondarie successive hanno il supporto EUS. EUS non è più supportato per RHEL < = 7,3.
>
> Altre informazioni sulla disponibilità di RHEL EUS sono disponibili [qui](https://access.redhat.com/support/policy/updates/errata).

Versione secondaria |Esempio di immagine EUS              |Stato EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat: RHEL: 7.4:7.4.2019041718 | Le immagini pubblicate il 2019 aprile e successive saranno EUS per impostazione predefinita|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Le immagini pubblicate il 2019 giugno e versioni successive saranno EUS per impostazione predefinita |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Le immagini pubblicate il 2019 e versioni successive saranno EUS per impostazione predefinita  |
RHEL 8,0      |N/D                            | Nessun EUS disponibile da Red Hat                               |





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
* Visualizzare l'elenco completo di [Immagini RHEL in Azure](./redhat-imagelist.md).
* Altre informazioni sull'infrastruttura di aggiornamento per Azure Red Hat [in questo articolo](https://aka.ms/rhui-update).
* Scopri di più sull' [offerta RHEL BYOS](./byos.md).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).
