---
title: Immagini Red Hat Enterprise Linux in Azure | Microsoft Docs
description: Informazioni sulle immagini Linux di Red Hat Enterprise in Microsoft Azure.Learn about Red Hat Enterprise Linux images in Microsoft Azure.
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
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239867"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Panoramica delle immagini Linux di Red Hat Enterprise

Questo articolo descrive le immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure Marketplace e i criteri relativi alla denominazione e alla conservazione.

Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata). Per informazioni dettagliate sui prezzi, vedere [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)di Azure.For pricing details, see Azure pricing calculator .

>[!IMPORTANT]
> Le immagini RHEL attualmente disponibili in Azure Marketplace supportano i modelli di licenza BYOS (Bring Your Own Subscription) o con pagamento in base al costo. Il [vantaggio dell'uso ibrido](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) di Azure e il passaggio dinamico tra BYOS e la licenza con pagamento in base al consumo non sono supportati. Per passare alla modalità di gestione licenze, è necessario ridistribuire la macchina virtuale dall'immagine corrispondente.

>[!NOTE]
> Per qualsiasi problema relativo alle immagini RHEL in Azure Marketplace, presentare un ticket di supporto a Microsoft.For any problem related to RHEL images in Azure Marketplace, file a support ticket with Microsoft.

## <a name="view-images-available-in-azure"></a>Visualizzare le immagini disponibili in AzureView images available in Azure

Quando si cerca "Red Hat" in Azure Marketplace o quando si crea una risorsa nell'interfaccia utente del portale di Azure, verrà visualizzato solo un subset di tutte le immagini RHEL disponibili. È sempre possibile ottenere il set completo di immagini di macchine virtuali disponibili usando l'interfaccia della riga di comando di Azure, PowerShell e l'API.

Per visualizzare il set completo di immagini Red Hat disponibili in Azure, eseguire il comando seguente:To see the full set of available Red Hat images in Azure, run the following command:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenzione di denominazione

Le immagini delle macchine virtuali in Azure sono organizzate per editore, offerta, SKU e versione. La combinazione di editore:offerta:SKU:versione è l'URN dell'immagine e identifica in modo univoco l'immagine da utilizzare.

Ad esempio, `RedHat:RHEL:8-LVM:8.1.20200318` si riferisce a un'immagine rHEL 8.1 partizionata da LVM basata il 18 marzo 2020.For example, refers to a RHEL 8.1 LVM-partitioned image built on March 18, 2020.

Di seguito è riportato un esempio di come creare una macchina virtuale RHEL 8.1.A sample of how to create a RHEL 8.1 VM is shown here.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Il percorso del collegamento "latest"

L'API REST di Azure consente l'uso del moniker "latest" per la versione anziché per la versione specifica. L'utilizzo delle disposizioni "più recenti" dell'ultima immagine disponibile per l'editore, l'offerta e lo SKU specificati.

Ad esempio, `RedHat:RHEL:8-LVM:latest` si riferisce all'ultima immagine partizionata Da LVM della famiglia RHEL 8.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> In generale, il confronto delle versioni per determinare la versione più recente segue le regole del [metodo CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Questo confronto tra le versioni dell'immagine viene eseguito confrontando i valori come oggetto [Version,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) non come stringa.

## <a name="rhel-6-image-types"></a>RHEL 6 tipi di immagine

Per le immagini RHEL 6.x, i tipi di immagine sono illustrati nella tabella seguente.

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 6.9) | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 6.9.2018010506) | Tutte le immagini rHEL 6.x standard seguono questa convenzione.
|RedHat | rel-byos | rel-raw69 | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 6.9.20181023) | Questa immagine è un'immagine BYOS RHEL 6.9.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 6.8.2017053118) | Questa immagine è un'immagine RHEL 6.8 per applicazioni SAP. È autorizzato ad accedere ai repository delle applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e data di pubblicazione (ad esempio, 6.7.2017053121) | Questa immagine è un'immagine RHEL 6.7 per l'immagine SAP HANA. È autorizzato ad accedere ai repository SAP HANA e ai repository RHEL di base.

## <a name="rhel-7-image-types"></a>Tipi di immagine RHEL 7

Per le immagini RHEL 7.x, esistono diversi tipi di immagine. La tabella seguente mostra i diversi set di immagini che offriamo. Per visualizzare un elenco completo, `az vm image list --publisher redhat --all`usare il comando dell'interfaccia della riga di comando di Azure.

>[!NOTE]
> Se non diversamente indicato, tutte le immagini sono partizionate LVM e si connettono ai normali repository RHEL. Ovvero, i repository non sono Extended Update Support (EUS) e non sono Update Services per SAP (E4S). In futuro, passiamo alla pubblicazione solo di immagini partizionate da LVM, ma siamo aperti a feedback su questa decisione. Per ulteriori informazioni sul supporto per gli aggiornamenti estesi e sui servizi di aggiornamento per SAP, vedere [Red Hat Enterprise Linux cycle.](https://access.redhat.com/support/policy/updates/errata)

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versione secondaria (ad esempio, 7.6) | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.6.2019102813) | Le immagini pubblicate prima di aprile 2019 sono allegate ai repository RHEL standard. Le immagini pubblicate dopo aprile 2019 sono allegate ai repository EUS di Red Hat per consentire il blocco delle versioni di una versione secondaria specifica. I clienti che desiderano archivi regolari devono utilizzare le immagini che contengono 7-LVM o 7-RAW nel valore SKU (i dettagli seguono). Le immagini RHEL 7.7 e successive sono partizionate l'LVM. Tutte le altre immagini di questa categoria sono partizionate raw.
|RedHat | RHEL | 7-RAW | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.6.2019102813) | Queste immagini sono partizionate raw (ad esempio, non sono stati aggiunti volumi logici).
|RedHat | RHEL | 7-RAW-CI | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.6.2019072418) | Queste immagini vengono partizionate non elaborata (ad esempio, non sono stati aggiunti volumi logici) e usano cloud-init per il provisioning.
|RedHat | RHEL | 7-LVM | Valori concatenati della versione secondaria RHEL e data di pubblicazione (ad esempio, 7.6.2019062414) | Queste immagini sono partizionate l'LVM.
|RedHat | rel-byos | rhel-lvm,raw | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.7.20190819) | Queste immagini sono le immagini RHEL 7 BYOS. Non sono collegati ad alcun repository e non addebiteranno la tariffa premium RHEL. Se sei interessato alle immagini RHEL BYOS, [richiedi l'accesso](https://aka.ms/rhel-byos). I valori SKU terminano con la versione secondaria e indicano se l'immagine è raw o LVM partizionata. Ad esempio, un valore SKU di rhel-lvm77 indica un'immagine RHEL 7.7 partizionata con LVM.
|RedHat | RHEL | RHEL-SAP | Valori concatenati della versione secondaria RHEL e data di pubblicazione (ad esempio, 7.6.2019071300) | Queste immagini sono RHEL per le immagini SAP. Hanno il diritto di accedere ai repository SAP HANA e Applications e ai repository RHEL E4S. La fatturazione include il premio RHEL e il premio SAP in cima alla tariffa di elaborazione di base.
|RedHat | RHEL | RHEL-SAP-HA | Valori concatenati della versione secondaria RHEL e data di pubblicazione (ad esempio, 7.6.2019062320) | Queste immagini sono RHEL per SAP con immagini High Availability e Update Services. Hanno il diritto di accedere ai repository SAP HANA e Applications e ai repository High Availability, nonché ai repository RHEL E4S. La fatturazione include il premio RHEL, il premio SAP e il premio High Availability oltre la tariffa di elaborazione di base.
|RedHat | RHEL | RHEL-HA | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.6.2019062019) | Queste immagini sono immagini RHEL che hanno anche il diritto di accedere al componente aggiuntivo Disponibilità elevata. Fanno pagare leggermente un extra sulla RHEL e base costo di calcolo a causa del premio aggiuntivo High Availability.
|RedHat | RHEL | RHEL-SAP-APPS | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 7.3.2017053118) | Queste immagini non sono aggiornate perché i repository SAP Applications e SAP HANA sono stati combinati nei repository SAP. Queste immagini sono immagini RHEL per applicazioni SAP. Hanno il diritto di accedere ai repository delle applicazioni SAP e ai repository RHEL di base.
|RedHat | RHEL | RHEL-SAP-HANA | Valori concatenati della versione secondaria RHEL e data di pubblicazione (ad esempio, 7.3.2018051421) | Queste immagini non sono aggiornate perché i repository SAP Applications e SAP HANA sono stati combinati nei repository SAP. Queste immagini sono RHEL per le immagini SAP HANA. Hanno il diritto di accedere ai repository SAP HANA e ai repository RHEL di base.

## <a name="rhel-8-image-types"></a>RHEL 8 tipi di immagine

I dettagli per i tipi di immagine RHEL 8 sono riportati di seguito.

|Editore | Offerta | Valore SKU | Versione | Dettagli
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 8.0.20191023) | Queste immagini sono immagini partizionate rHEL 8 LVM collegate ai repository Red Hat standard.
|RedHat | RHEL | 8-gen2 | Valori concatenati della versione secondaria RHEL e la data di pubblicazione (ad esempio, 8.0.20191024) | Queste immagini sono immagini partizionate da LVM Hyper-V Generation 2 RHEL 8 collegate ai repository standard Red Hat. Per altre informazioni sulle macchine virtuali di generazione 2 in Azure, vedere Supporto per le macchine virtuali di generazione 2 in Azure.For more information about Generation 2 VMs in Azure, see [Support for Generation 2 VMs on Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

## <a name="rhel-longer-support-add-ons"></a>RHEL supporto più lungo add-on

### <a name="extended-update-support"></a>Supporto per l'aggiornamento esteso

A partire da aprile 2019, sono disponibili immagini RHEL che sono collegate ai repository EUS per impostazione predefinita. Ulteriori informazioni su RHEL EUS sono disponibili nella [documentazione di Red Hat.](https://access.redhat.com/articles/rhel-eus)

Il passaggio ai repository EUS è possibile ed è supportato. Per istruzioni su come passare la macchina virtuale a EUS e ulteriori informazioni sulle date di fine ciclo di vita di EUS, vedere [RHEL EUS e il blocco della versione delle macchine virtuali RHEL](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS non è supportato su RHEL Extra. Ciò significa che se installi un pacchetto che di solito è disponibile dal canale RHEL Extras, non sarai in grado di farlo mentre sei su EUS. Per ulteriori informazioni sul ciclo di vita del prodotto Red Hat Extras, vedere [Red Hat Enterprise Linux Extras](https://access.redhat.com/support/policy/updates/extras/)life cycle .

#### <a name="differentiate-between-regular-and-eus-images"></a>Differenziare tra immagini regolari ed EUS

I clienti che desiderano utilizzare immagini associate ai repository EUS devono utilizzare l'immagine RHEL che contiene un numero di versione secondario RHEL nello SKU.

Ad esempio, è possibile che siano disponibili le due immagini RHEL 7.4 seguenti.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

In questo `RedHat:RHEL:7.6:7.6.2019102813` caso, è collegato ai repository EUS per impostazione predefinita. Il valore SKU è 7.4.The SKU value is 7.4. Ed `RedHat:RHEL:7-LVM:7.6.2019062414` è collegato a repository non EUS per impostazione predefinita. Il valore SKU è 7-LVM.

Per utilizzare archivi regolari (non EUS), utilizzare un'immagine che non contenga un numero di versione secondario nello SKU.

#### <a name="rhel-images-with-eus"></a>Immagini RHEL con EUS

Le informazioni contenute nella tabella seguente si applicano alle immagini RHEL connesse ai repository EUS.

>[!NOTE]
> Al momento della scrittura, solo RHEL 7.4 e versioni successive secondarie hanno il supporto EUS. EUS non è più supportato per il <RHEL 7,3.
>
> Per ulteriori informazioni sulla disponibilità di RHEL EUS, vedere [Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata).

Versione secondaria |Esempio di immagine EUS              |Stato EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |Cappello Rosso:RHEL:7.4:7.4.2019041718 | Le immagini pubblicate ad aprile 2019 e successive sono EUS per impostazione predefinita.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Le immagini pubblicate a giugno 2019 e successive sono EUS per impostazione predefinita. |
RHEL 7.6      |Cappello Rosso:RHEL:7.6:7.6.2019052206 | Le immagini pubblicate a maggio 2019 e successive sono EUS per impostazione predefinita. |
RHEL 8.0      |N/D                            | Nessun EUS è disponibile da Red Hat.                               |

### <a name="update-services-for-sap"></a>Servizi di aggiornamento per SAP

Le immagini RHEL per SAP più recenti verranno connesse alle sottoscrizioni di Update Services for SAP Solutions (E4S). Per ulteriori informazioni su E4S, vedere la [documentazione](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)di Red Hat .

#### <a name="rhel-images-with-e4s"></a>Immagini RHEL con E4S

Le immagini delle seguenti offerte create dopo dicembre 2019 sono collegate ai repository E4S:

* RHEL-SAP (RHEL per SAP)
* RHEL-SAP-HA (RHEL per SAP con servizi di disponibilità elevata e aggiornamenti)

## <a name="other-available-offers-and-skus"></a>Altre offerte e SKU disponibili

L'elenco completo delle offerte e degli SKU disponibili potrebbe includere immagini aggiuntive oltre a quelle elencate nella tabella precedente. Un esempio è `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Queste offerte potrebbero essere utilizzate per fornire supporto per soluzioni specifiche del marketplace. Oppure potrebbero essere pubblicati a scopo di anteprima e test. Essi potrebbero essere modificati o rimossi in qualsiasi momento senza preavviso. Non utilizzarli a meno che la loro presenza non sia documentata pubblicamente da Microsoft o Red Hat.

## <a name="publishing-policy"></a>Criteri di pubblicazione

Microsoft e Red Hat aggiornano le immagini quando vengono rilasciate nuove versioni secondarie, necessarie per risolvere vulnerabilità ed esposizioni comuni specifiche o per modifiche o aggiornamenti occasionali della configurazione. Ci sforziamo di fornire immagini aggiornate il prima possibile entro tre giorni lavorativi dopo il rilascio o la disponibilità di una correzione CVE.

Aggiorniamo solo la versione secondaria corrente in una determinata famiglia di immagini. Con il rilascio di una versione secondaria più recente, interrompiamo l'aggiornamento della versione secondaria precedente. Ad esempio, con il rilascio di RHEL 7.6, le immagini RHEL 7.5 non vengono più aggiornate.

>[!NOTE]
> Le macchine virtuali di Azure attive di cui è stato eseguito il provisioning dalle immagini con pagamento in base al client vengono connesse all'interfaccia di accesso a rime di Azure e possono ricevere aggiornamenti e correzioni non appena vengono rilasciate da Red Hat e replicate nell'interfaccia DI replica di Azure RHUI. La tempistica è di solito meno di 24 ore dopo l'uscita ufficiale di Red Hat. Queste macchine virtuali non richiedono una nuova immagine pubblicata per ottenere gli aggiornamenti. I clienti hanno il controllo completo su quando avviare l'aggiornamento.

## <a name="image-retention-policy"></a>Criteri di conservazione delle immagini

La politica attuale è quella di mantenere tutte le immagini pubblicate in precedenza. Ci riserviamo il diritto di rimuovere le immagini che potrebbero causare problemi di qulasiasi tipo. Ad esempio, le immagini con configurazioni non corrette a causa di successivi aggiornamenti della piattaforma o del componente potrebbero essere rimossi. Le immagini che potrebbero essere rimosse seguono i criteri correnti di Azure Marketplace per fornire notifiche fino a 30 giorni prima della rimozione dell'immagine.

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare l'elenco completo delle immagini RHEL in Azure, vedere [Immagini RHEL (Red Hat Enterprise Linux) disponibili in Azure.](./redhat-imagelist.md)
* Per altre informazioni sull'infrastruttura di aggiornamento di Azure Red Hat, vedere Infrastruttura di aggiornamento Red Hat per macchine virtuali RHEL su richiesta in Azure.To learn more about the Azure Red Hat Update Infrastructure, see [Red Hat Update Infrastructure for on-demand RHEL VMs in Azure.](https://aka.ms/rhui-update)
* Per altre informazioni sull'offerta RHEL BYOS, vedere [Red Hat Enterprise Linux bring-your-own-subscription Gold Images in Azure](./byos.md).
* Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata).
