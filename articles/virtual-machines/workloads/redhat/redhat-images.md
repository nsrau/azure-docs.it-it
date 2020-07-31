---
title: Panoramica delle immagini Red Hat Enterprise Linux in Azure
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 9aa0ca41f63da94e2dedaffe65ea518b8adff0dc
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439466"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Cenni preliminari sulle immagini Red Hat Enterprise Linux

Questo articolo descrive le immagini del Red Hat Enterprise Linux disponibile (RHEL) in Azure Marketplace e i criteri relativi alla denominazione e alla conservazione.

Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata). Per informazioni dettagliate sui prezzi, vedere [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Le immagini RHEL attualmente disponibili in Azure Marketplace supportano i modelli di licenza Bring-your-own-Subscription (BYOS) o con pagamento in base al consumo. Il [vantaggio Azure Hybrid use](../../windows/hybrid-use-benefit-licensing.md) e il passaggio dinamico tra BYOS e le licenze con pagamento in base al consumo non sono supportati. Per cambiare la modalità di gestione delle licenze, è necessario ridistribuire la macchina virtuale dall'immagine corrispondente.

>[!NOTE]
> Per eventuali problemi relativi alle immagini RHEL in Azure Marketplace, è possibile archiviare un ticket di supporto con Microsoft.

## <a name="view-images-available-in-azure"></a>Visualizzare le immagini disponibili in Azure

Quando si cerca "Red Hat" in Azure Marketplace o quando si crea una risorsa nell'interfaccia utente di portale di Azure, viene visualizzato solo un subset di tutte le immagini RHEL disponibili. È sempre possibile ottenere il set completo di immagini di VM disponibili usando l'interfaccia della riga di comando di Azure, PowerShell e l'API.

Per visualizzare il set completo di immagini Red Hat disponibili in Azure, eseguire il comando seguente:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenzione di denominazione

Le immagini di VM in Azure sono organizzate in base all'editore, all'offerta, allo SKU e alla versione. La combinazione di editore:offerta:SKU:versione è l'URN dell'immagine e identifica in modo univoco l'immagine da utilizzare.

Ad esempio, `RedHat:RHEL:8-LVM:8.1.20200318` fa riferimento a un'immagine RHEL 8,1 LVM partizionata creata il 18 marzo 2020.

Di seguito è riportato un esempio di come creare una VM RHEL 8,1.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Il percorso del collegamento "latest"

L'API REST di Azure consente di usare il moniker "Latest" per la versione anziché per la versione specifica. L'uso di "Latest" effettua il provisioning dell'immagine più recente disponibile per l'editore, l'offerta e lo SKU specificati.

`RedHat:RHEL:8-LVM:latest`Si riferisce ad esempio alla versione più recente dell'immagine partizionata LVM 8, disponibile.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> In generale, il confronto delle versioni per determinare la versione più recente segue le regole del [metodo CompareTo](/dotnet/api/system.version.compareto?view=netcore-3.1#system_version_compareto_system_version_).
Il confronto tra le versioni dell'immagine viene eseguito confrontando i valori come oggetto [Version](/dotnet/api/system.version.-ctor?view=netframework-4.8) , non come stringa.

## <a name="rhel-6-image-types"></a>Tipi di immagine RHEL 6

Per le immagini RHEL 6. x, i tipi di immagine sono riportati nella tabella seguente.

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 6,9) | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 6.9.2018010506) | Tutte le immagini RHEL 6. x standard seguono questa convenzione.
|RedHat | rhel-byos | RHEL-raw69 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 6.9.20181023) | Questa immagine è un'immagine RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 6.8.2017053118) | Questa immagine è un'immagine RHEL 6,8 per le applicazioni SAP. È autorizzato ad accedere ai repository di applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 6.7.2017053121) | Questa immagine è un RHEL 6,7 per SAP HANA immagine. È autorizzato ad accedere ai repository SAP HANA e ai repository RHEL di base.

## <a name="rhel-7-image-types"></a>Tipi di immagine RHEL 7

Per le immagini RHEL 7. x, sono disponibili diversi tipi di immagine. La tabella seguente illustra i diversi set di immagini disponibili. Per visualizzare un elenco completo, usare il comando dell'interfaccia della riga di comando di Azure `az vm image list --publisher redhat --all` .

>[!NOTE]
> Se non diversamente specificato, tutte le immagini sono partizionate in LVM e si connettono a repository RHEL regolari. Ovvero, i repository non sono il supporto per gli aggiornamenti estesi (EUS) e non sono Update Services for SAP (E4S). In futuro, stiamo passando alla pubblicazione solo di immagini con partizionamento LVM, ma siamo aperti a commenti e suggerimenti su questa decisione. Per altre informazioni sul supporto degli aggiornamenti estesi e sui servizi di aggiornamento per SAP, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata).

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 7,6) | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019102813) | Le immagini pubblicate prima del 2019 aprile sono collegate ai repository RHEL standard. Le immagini pubblicate dopo il 2019 aprile vengono collegate ai repository EUS di Red Hat per consentire il blocco della versione di una specifica versione secondaria. I clienti che desiderano repository regolari devono usare le immagini che contengono 7-LVM o 7-RAW nel valore SKU (i dettagli seguono). RHEL 7,7 e le immagini successive sono partizionate in LVM. Tutte le altre immagini in questa categoria sono partizionate non elaborate.
|RedHat | RHEL | 7-RAW | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019102813) | Queste immagini sono partizionate non elaborate, ad esempio non è stato aggiunto alcun volume logico.
|RedHat | RHEL | 7-RAW-CI | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019072418) | Queste immagini sono partizionate non elaborate, ad esempio non è stato aggiunto alcun volume logico, e usano cloud-init per il provisioning.
|RedHat | RHEL | 7-LVM | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019062414) | Queste immagini sono partizionate in LVM.
|RedHat | rhel-byos | RHEL-{LVM, RAW} | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.7.20190819) | Queste immagini sono immagini RHEL 7 BYOS. Non sono collegati ad alcun repository e non addebitano la tariffa Premium RHEL. Se si è interessati alle immagini RHEL BYOS, [richiedere l'accesso](https://aka.ms/rhel-byos). I valori di SKU terminano con la versione secondaria e indicano se l'immagine è non elaborata o LVM partizionata. Ad esempio, un valore SKU di RHEL-lvm77 indica un'immagine RHEL 7,7 partizionata LVM.
|RedHat | RHEL | RHEL-SAP | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019071300) | Queste immagini sono RHEL per le immagini SAP. Hanno diritto ad accedere ai repository di SAP HANA e applicazioni, nonché ai repository E4S di RHEL. La fatturazione include RHEL Premium e SAP Premium oltre alla tariffa di calcolo di base.
|RedHat | RHEL | RHEL-SAP-HA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019062320) | Queste immagini sono RHEL per SAP con la disponibilità elevata e le immagini dei servizi di aggiornamento. Hanno diritto ad accedere ai repository di SAP HANA e applicazioni e ai repository a disponibilità elevata, nonché ai repository E4S RHEL. La fatturazione include RHEL Premium, SAP Premium e High Availability Premium oltre alla tariffa di calcolo di base.
|RedHat | RHEL | RHEL-HA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.6.2019062019) | Queste immagini sono immagini RHEL che hanno anche il diritto di accedere al componente aggiuntivo di disponibilità elevata. L'addebito è leggermente più alto rispetto a RHEL e alla tariffa di calcolo di base a causa del componente aggiuntivo Premium a disponibilità elevata.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.3.2017053118) | Queste immagini non sono aggiornate perché le applicazioni SAP e i repository di SAP HANA sono stati combinati nei repository SAP. Queste immagini sono RHEL per le immagini di applicazioni SAP. Hanno diritto ad accedere ai repository di applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 7.3.2018051421) | Queste immagini non sono aggiornate perché le applicazioni SAP e i repository di SAP HANA sono stati combinati nei repository SAP. Queste immagini sono immagini RHEL for SAP HANA. Hanno diritto ad accedere ai repository SAP HANA e ai repository RHEL di base.

## <a name="rhel-8-image-types"></a>Tipi di immagine RHEL 8

>[!NOTE]
> Red Hat consiglia di usare le estirpazioni per configurare i parametri della riga di comando kernel in RHEL 8 +. Altre informazioni sono disponibili [qui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Di seguito sono riportati i dettagli relativi ai tipi di immagine RHEL 8.

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 8.0.20191023) | Queste immagini sono immagini con partizionamento in RHEL 8, connesse ai repository standard di Red Hat.
|RedHat | RHEL | 8-Gen2 | Valori concatenati della versione secondaria RHEL e della data di pubblicazione (ad esempio, 8.0.20191024) | Queste immagini sono immagini partizionate di Hyper-V di seconda generazione (LVM), che sono connesse ai repository standard di Red Hat. Per altre informazioni sulle macchine virtuali di seconda generazione in Azure, vedere [supporto per le macchine virtuali di seconda generazione in Azure](../../linux/generation-2.md).

## <a name="rhel-longer-support-add-ons"></a>Componenti aggiuntivi supportati da RHEL

### <a name="extended-update-support"></a>Supporto per aggiornamenti estesi

A partire dal 2019 aprile, sono disponibili immagini RHEL associate ai repository EUS per impostazione predefinita. Altre informazioni su RHEL EUS sono disponibili nella [documentazione di Red Hat](https://access.redhat.com/articles/rhel-eus).

Il trasferimento a repository EUS è possibile ed è supportato. Per istruzioni su come passare la macchina virtuale a EUS e altre informazioni sulle date di scadenza del supporto per EUS, vedere la pagina relativa alle [macchine virtuali RHEL EUS e con blocco della versione](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS non è supportato in extra RHEL. Ciò significa che se si installa un pacchetto che in genere è disponibile dal canale extra RHEL, non sarà possibile eseguire questa operazione in EUS. Per ulteriori informazioni sul ciclo di vita del prodotto Red Hat Extras, vedere [Red Hat Enterprise Linux ciclo di vita extra](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Distinguere tra immagini regolari e EUS

I clienti che vogliono usare le immagini collegate ai repository EUS devono usare l'immagine RHEL contenente un numero di versione secondario RHEL nello SKU.

Ad esempio, è possibile visualizzare le seguenti due immagini RHEL 7,4 disponibili.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

In questo caso, `RedHat:RHEL:7.6:7.6.2019102813` è associato ai repository EUS per impostazione predefinita. Il valore SKU è 7,4. Per `RedHat:RHEL:7-LVM:7.6.2019062414` impostazione predefinita, è associato a repository non EUS. Il valore SKU è 7-LVM.

Per usare i repository regolari (non-EUS), usare un'immagine che non contiene un numero di versione secondario nello SKU.

#### <a name="rhel-images-with-eus"></a>Immagini RHEL con EUS

Le informazioni riportate nella tabella seguente sono valide per le immagini RHEL connesse a repository EUS.

>[!NOTE]
> Al momento della stesura di questo articolo, solo RHEL 7,4 e versioni secondarie successive hanno il supporto EUS. EUS non è più supportato per RHEL <= 7,3.
>
> Per altre informazioni sulla disponibilità di RHEL EUS, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata).

Versione secondaria |Esempio di immagine EUS              |Stato EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat: RHEL: 7.4:7.4.2019041718 | Le immagini pubblicate il 2019 aprile e versioni successive sono EUS per impostazione predefinita.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Le immagini pubblicate il 2019 giugno e versioni successive sono EUS per impostazione predefinita. |
RHEL 7.6      |RedHat: RHEL: 7.6:7.6.2019052206 | Le immagini pubblicate il 2019 e versioni successive sono EUS per impostazione predefinita. |
RHEL 8,0      |N/D                            | Nessun EUS è disponibile da Red Hat.                               |

### <a name="update-services-for-sap"></a>Aggiornare i servizi per SAP

La versione più recente di RHEL per le immagini SAP verrà connessa alle sottoscrizioni Update Services for SAP Solutions (E4S). Per ulteriori informazioni su E4S, vedere la [documentazione](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)di Red Hat.

#### <a name="rhel-images-with-e4s"></a>Immagini RHEL con E4S

Le immagini delle seguenti offerte create dopo il 2019 dicembre sono connesse ai repository E4S:

* RHEL-SAP (RHEL per SAP)
* RHEL-SAP-HA (RHEL for SAP con disponibilità elevata e servizi di aggiornamento)

## <a name="other-available-offers-and-skus"></a>Altre offerte e SKU disponibili

L'elenco completo delle offerte e degli SKU disponibili potrebbe includere immagini aggiuntive oltre a quelle elencate nella tabella precedente. Un esempio è `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Queste offerte possono essere usate per fornire supporto per soluzioni specifiche del Marketplace. Oppure possono essere pubblicati per le anteprime e i test. Potrebbero essere modificati o rimossi in qualsiasi momento senza preavviso. Non utilizzarli a meno che la loro presenza non sia documentata pubblicamente da Microsoft o Red Hat.

## <a name="publishing-policy"></a>Criteri di pubblicazione

Microsoft e Red Hat aggiornano le immagini quando vengono rilasciate nuove versioni secondarie, come richiesto per risolvere vulnerabilità e esposizioni comuni specifiche (CVEs) o per modifiche o aggiornamenti della configurazione occasionali. Ci impegniamo a fornire le immagini aggiornate il prima possibile entro tre giorni lavorativi dopo un rilascio o la disponibilità di una correzione CVE.

Viene aggiornata solo la versione secondaria corrente in una determinata famiglia di immagini. Con il rilascio di una versione secondaria più recente, interrompiamo l'aggiornamento della versione secondaria precedente. Ad esempio, con il rilascio di RHEL 7,6, le immagini RHEL 7,5 non vengono più aggiornate.

>[!NOTE]
> Le macchine virtuali di Azure attive di cui è stato effettuato il provisioning dalle immagini con pagamento in base al consumo di RHEL sono connesse alla RHUI di Azure e possono ricevere aggiornamenti e correzioni non appena vengono rilasciate da Red Hat e replicate in Azure RHUI. Il tempo è in genere inferiore a 24 ore dopo la versione ufficiale di Red Hat. Queste macchine virtuali non richiedono una nuova immagine pubblicata per ottenere gli aggiornamenti. I clienti hanno il controllo completo su quando avviare l'aggiornamento.

## <a name="image-retention-policy"></a>Criteri di conservazione delle immagini

I criteri correnti consentono di proteggere tutte le immagini precedentemente pubblicate. Ci riserviamo il diritto di rimuovere le immagini che potrebbero causare problemi di qulasiasi tipo. Ad esempio, è possibile che vengano rimosse le immagini con configurazioni non corrette a causa di aggiornamenti successivi della piattaforma o del componente. Le immagini che potrebbero essere rimosse seguono i criteri correnti di Azure Marketplace per fornire notifiche fino a 30 giorni prima della rimozione dell'immagine.

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare l'elenco completo di immagini RHEL in Azure, vedere le [immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure](./redhat-imagelist.md).
* Per altre informazioni sull'infrastruttura di Azure Red Hat Update, vedere l'articolo relativo all' [infrastruttura di aggiornamento di Red Hat per macchine virtuali RHEL su richiesta in Azure](https://aka.ms/rhui-update).
* Per altre informazioni sull'offerta BYOS di RHEL, vedere [Red Hat Enterprise Linux immagini Gold Bring your own Subscription in Azure](./byos.md).
* Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata).
