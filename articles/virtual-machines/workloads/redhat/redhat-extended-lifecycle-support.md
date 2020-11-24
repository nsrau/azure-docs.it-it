---
title: Supporto per ciclo di vita esteso Red Hat Enterprise Linux
description: Scopri di più sull'aggiunta del supporto per Red Hat Enterprise Extended Lifecycle
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744042"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Supporto del ciclo di vita esteso Red Hat Enterprise Linux (RHEL)
Questo articolo fornisce informazioni sul supporto del ciclo di vita esteso per le immagini di Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Ciclo di vita Red Hat Enterprise Linux 6
A partire dal 30 novembre 2020, Red Hat Enterprise Linux 6 raggiungerà la fine della fase di manutenzione. La fase di manutenzione è seguita dalla fase di durata estesa. Man mano che Red Hat Enterprise Linux 6 esegue le transizioni fuori dalle fasi di manutenzione e completa, si consiglia vivamente di eseguire l'aggiornamento a Red Hat Enterprise Linux 7 o 8. Se i clienti devono rimanere in Red Hat Enterprise Linux 6, è consigliabile aggiungere il Red Hat Enterprise Linux componente aggiuntivo del supporto per il ciclo di vita esteso (ELS).

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Passaggi per aggiungere il supporto del ciclo di vita esteso nelle VM con pagamento in base al consumo del Marketplace
1. Compilare il [modulo Els disponibile qui](https://aka.ms/els-form) con i dettagli di contatto e le informazioni sulla sottoscrizione delle macchine virtuali per cui si vuole aggiungere il supporto Els. I dettagli relativi ai prezzi aggiuntivi sono disponibili anche nel form.
1. Il team di Red Hat Enterprise Linux di Azure contatterà l'utente con un elenco di macchine virtuali per il supporto ELS aggiuntivo entro 1-2 giorni lavorativi. Esaminare l'elenco e rispondere all'accordo sui prezzi aggiuntivi.
1. Il team di Red Hat Enterprise Linux di Azure condividerà i passaggi necessari per aggiungere il pacchetto client ELS alle macchine virtuali. Attenersi alla procedura, che verrà fornita nel messaggio di posta elettronica, per continuare a ricevere la manutenzione del software (correzioni di bug e sicurezza) e il supporto per Red Hat Enterprise Linux 6.

> [!Note]
> Non condividere i passaggi per l'uso di RHEL ELS Add on con utenti esterni all'organizzazione. Contattare per AzureRedHatELS@microsoft.com ottenere supporto o per eventuali domande aggiuntive.

## <a name="frequently-asked-questions"></a>Domande frequenti

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Sto eseguendo Red Hat Enterprise Linux 6 e non è possibile eseguire la migrazione a una versione successiva in questo momento. Quali sono le opzioni a disposizione?
* Continuare a eseguire Red Hat Enterprise Linux 6 e acquistare i repository del supporto per il ciclo di vita estesi (ELS) Add-On per continuare a ricevere supporto tecnico e manutenzione software limitati (vedere il processo di aggiornamento e i dettagli sui prezzi indicati di seguito).
* Eseguire la migrazione a Red Hat Enterprise Linux 7 o 8 non appena possibile.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Qual è l'addebito aggiuntivo per l'uso di Red Hat Enterprise Linux componente aggiuntivo per il supporto del ciclo di vita esteso (ELS)?
I costi correlati al supporto del ciclo di vita esteso sono disponibili con il [modulo Els](https://aka.ms/els-form)


## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare l'elenco completo di immagini RHEL in Azure, vedere le [immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure](./redhat-imagelist.md).
* Per altre informazioni sull'infrastruttura di Azure Red Hat Update, vedere l'articolo relativo all' [infrastruttura di aggiornamento di Red Hat per macchine virtuali RHEL su richiesta in Azure](./redhat-rhui.md).
* Per altre informazioni sull'offerta BYOS di RHEL, vedere [Red Hat Enterprise Linux immagini Gold Bring your own Subscription in Azure](./byos.md).
* Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere [Red Hat Enterprise Linux ciclo di vita](https://access.redhat.com/support/policy/updates/errata).