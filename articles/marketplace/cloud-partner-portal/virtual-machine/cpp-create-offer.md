---
title: Creare un'offerta di macchina virtuale in Azure Marketplace
description: Elenca i passaggi necessari per creare una nuova offerta di macchina virtuale (VM, Virtual Machine) per Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1e10bd22b91b51fb811601e49fad06d8f8b30127
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817091"
---
# <a name="create-virtual-machine-offer"></a>Creare l'offerta di una macchina virtuale

Questa sezione elenca i passaggi necessari per creare una nuova richiesta di offerta di macchina virtuale (VM, Virtual Machine) per Azure Marketplace.  Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace ed è associata a uno o più SKU.  Un'offerta di macchina virtuale è composta dai gruppi di risorse e servizi di supporto seguenti: 

![Risorse per un'offerta di macchina virtuale](./media/publishvm_002.png)

dove:

|  **Gruppo di risorse**   |  **Descrizione**  |
|  ---------------   |  ---------------  |
|    SKU            |  La più piccola unità acquistabile di un'offerta. A una singola offerta (classe di prodotti) possono essere associati più SKU, per distinguere tra funzionalità supportate, tipi di immagini di macchina virtuale e modelli di fatturazione diversi. |
|  Marketplace       | Contiene specifiche e asset di marketing, legali e di gestione dei clienti potenziali.  <ul><li> Le risorse di marketing includono il nome dell'offerta, la descrizione e i loghi</li> <li> Le risorse legali includono l'informativa sulla privacy, le condizioni per l'utilizzo e altra documentazione legale</li>  <li> I criteri di gestione dei clienti potenziali consentono di specificare come gestire i clienti potenziali dal portale Azure Marketplace per gli utenti finali.</li> </ul> |
| Supporto            | Contiene informazioni sui criteri e sui contatti di supporto |
| Test drive         | Definisce le risorse che consentono agli utenti finali di testare l'offerta prima di acquistarla |
|  |  |


## <a name="new-offer-form"></a>Modulo Nuova offerta

Dopo l'accesso al [portale Cloud Partner](https://cloudpartner.azure.com/) fare clic sulla voce **+ Nuova offerta** sulla barra dei menu a sinistra. Nel menu risultante, fare clic su **Macchine virtuali** per visualizzare il modulo **Nuova offerta** e avviare il processo di definizione delle risorse per una nuova offerta di macchina virtuale. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Selezione dell'interfaccia utente della nuova offerta di macchina virtuale](./media/publishvm_003.png)

> [!WARNING]
> Se l'opzione **Macchine virtuali** non viene visualizzata o non è abilitata, l'account non ha l'autorizzazione per creare questo tipo di offerta.  Verificare di aver soddisfatto tutti i [prerequisiti](./cpp-prerequisites.md) per questo tipo di offerta, tra cui la registrazione per un account sviluppatore.


## <a name="next-steps"></a>Passaggi successivi

Gli argomenti successivi di questa sezione illustrano le schede nella pagina **Nuova offerta** per il tipo di offerta relativo a una macchina virtuale.  Ogni articolo illustra come usare la scheda descritta per definire i gruppi di risorse e i servizi di supporto per la nuova offerta di macchina virtuale.

- [Scheda Impostazioni dell'offerta](./cpp-offer-settings-tab.md)
- [Scheda SKU](./cpp-skus-tab.md)
- [Scheda Test Drive](./cpp-test-drive-tab.md)
- [Scheda Marketplace](./cpp-marketplace-tab.md)
- [Scheda Supporto](./cpp-support-tab.md)
