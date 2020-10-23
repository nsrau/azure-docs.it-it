---
title: Ripristino da un errore del dispositivo Pro Azure Stack Edge
description: Viene descritto come eseguire il ripristino da un dispositivo Azure Stack Edge Pro con errori.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 91efdac9d17e679226f10012bbff9b5b6676ff0e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205579"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Ripristino da un dispositivo GPU Pro Azure Stack Edge non riuscito 

Questo articolo descrive come eseguire il ripristino da un errore non tollerabile sul dispositivo GPU Azure Stack Edge Pro. Un errore non tollerabile nel dispositivo GPU Pro Azure Stack Edge richiede una sostituzione del dispositivo.

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di disporre degli elementi seguenti:

- È stato contattato supporto tecnico Microsoft per quanto riguarda l'errore del dispositivo ed è stata consigliata una sostituzione del dispositivo. 
- Eseguire il backup della configurazione del dispositivo come descritto in [preparare un errore del dispositivo](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Configurare il dispositivo sostitutivo

Quando il dispositivo rileva un errore non tollerabile, è necessario ordinare un dispositivo sostitutivo. I passaggi di configurazione per il dispositivo sostitutivo rimangono invariati. 

Recuperare le informazioni di configurazione del dispositivo di cui è stato eseguito il backup dal dispositivo che ha avuto esito negativo. Queste informazioni vengono usate per configurare il dispositivo sostitutivo.  

Per configurare il dispositivo sostitutivo, attenersi alla procedura seguente:

1. Raccogliere le informazioni necessarie nell' [elenco di controllo della distribuzione](azure-stack-edge-gpu-deploy-checklist.md). È necessario usare le informazioni salvate dalla configurazione del dispositivo precedente. 
1. Ordinare un nuovo dispositivo della stessa configurazione di quello che ha avuto esito negativo.  Per inserire un ordine, [creare una nuova risorsa Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md#) nel portale di Azure.
1. [Decomprimere](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [montare su rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cablare il dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Connettersi all'interfaccia utente locale del dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configurare la rete usando gli stessi indirizzi IP usati per il dispositivo precedente. Questo riduce al minimo l'impatto su tutti i computer client usati nell'ambiente. Vedere come [configurare le impostazioni di rete](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Assegnare lo stesso nome di dispositivo e dominio DNS del dispositivo precedente. Ciò garantisce che i client possano usare lo stesso nome di dispositivo per comunicare con il nuovo dispositivo. Vedere come [configurare l'impostazione del dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configurare i certificati nel nuovo dispositivo nello stesso modo usato per il dispositivo precedente. Tenere presente che il nuovo dispositivo ha un nuovo numero di serie del nodo. Se sono stati usati certificati personalizzati sul dispositivo precedente, sarà necessario ottenere un nuovo certificato del nodo. Vedere come [configurare i certificati](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Ottenere la chiave di attivazione dalla portale di Azure e attivare il nuovo dispositivo. Vedere come [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).

A questo punto è possibile distribuire i carichi di lavoro in esecuzione nel dispositivo precedente.

## <a name="restore-edge-cloud-shares"></a>Ripristinare le condivisioni cloud perimetrali

Seguire questa procedura per ripristinare i dati nelle condivisioni cloud perimetrali sul dispositivo:

1. [Aggiungere condivisioni](azure-stack-edge-j-series-manage-shares.md#add-a-share) con gli stessi nomi di condivisione creati in precedenza nel dispositivo in cui si è verificato l'errore. Assicurarsi che durante la creazione delle condivisioni, **selezionare il contenitore BLOB** sia impostato su **Usa opzione esistente** e quindi selezionare il contenitore usato con il dispositivo precedente.
1. [Aggiungere gli utenti](azure-stack-edge-j-series-manage-users.md#add-a-user) che hanno avuto accesso al dispositivo precedente.
1. [Aggiungere gli account di archiviazione](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) associati alle condivisioni precedenti nel dispositivo. Durante la creazione di account di archiviazione Edge selezionare da un contenitore esistente e puntare al contenitore mappato all'account di archiviazione di Azure mappato al dispositivo precedente. Tutti i dati del dispositivo scritti nell'account di archiviazione perimetrale sul dispositivo precedente sono stati caricati nel contenitore di archiviazione selezionato nell'account di archiviazione di Azure mappato.
1. [Aggiornare i](azure-stack-edge-j-series-manage-shares.md#refresh-shares) dati di condivisione da Azure. In questo modo si eseguirà il pull di tutti i dati cloud dal contenitore esistente alle condivisioni.

## <a name="restore-edge-local-shares"></a>Ripristinare le condivisioni locali perimetrali

Per preparare un potenziale errore del dispositivo, è possibile che sia stata distribuita una delle soluzioni di backup seguenti per proteggere i dati delle condivisioni locali dai carichi di lavoro Kubernetes o Internet:

| Software di terze parti           | Riferimento alla soluzione                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Per informazioni dettagliate, contattare Cohesity.          |
| CommVault                      | https://www.commvault.com/azure <br> Per informazioni dettagliate, contattare CommVault. |
| Veritas                        | http://veritas.com/azure <br> Per informazioni dettagliate, contattare Veritas.   |

Dopo che il dispositivo sostitutivo è stato completamente configurato, abilitare il dispositivo per l'archiviazione locale. 

Per ripristinare i dati dalle condivisioni locali, attenersi alla procedura seguente: 

1. [Configurare il calcolo nel dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Aggiungere nuovamente una condivisione locale](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) .
1. Eseguire la procedura di ripristino fornita dalla soluzione di protezione dei dati preferita. Vedere i riferimenti della tabella precedente.

## <a name="restore-vm-files-and-folders"></a>Ripristinare i file e le cartelle della macchina virtuale

Per preparare un potenziale errore del dispositivo, è possibile che sia stata distribuita una delle soluzioni di backup seguenti per proteggere i dati nelle macchine virtuali:



| Soluzioni di backup        | Sistema operativo supportato   | Informazioni di riferimento                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente di Servizi di ripristino di Microsoft Azure (MARS) per backup di Azure | Windows        | [Informazioni sull'agente MARS](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Riepilogo della soluzione di integrazione, backup e ripristino Microsoft Azure](https://www.cohesity.com/solution/cloud/azure) <br>Per informazioni dettagliate, contattare Cohesity.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br> Per informazioni dettagliate, contattare CommVault.
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Per informazioni dettagliate, contattare Veritas.                    |

Una volta completata la configurazione del dispositivo sostitutivo, è possibile ridistribuire le VM con l'immagine di macchina virtuale usata in precedenza. 

Per ripristinare i dati nelle VM, seguire questa procedura:
 
1. [Distribuire una macchina virtuale da un'immagine di macchina virtuale](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) nel dispositivo. 
1. Installare la soluzione di protezione dei dati preferita nella macchina virtuale.
1. Eseguire la procedura di ripristino fornita dalla soluzione di protezione dei dati preferita. Vedere i riferimenti della tabella precedente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [restituire un dispositivo Azure stack Edge Pro](azure-stack-edge-return-device.md).
