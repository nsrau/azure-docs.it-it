---
title: File di inclusione
description: File di inclusione
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 04081a514d9b96a5289594a730ec0519e6ee0b01
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36329791"
---
## <a name="provision-the-solution"></a>Effettuare il provisioning della soluzione

Se nel proprio account non è già stato effettuato il provisioning della soluzione preconfigurata per il monitoraggio remoto:

1. Accedere ad [azureiotsuite.com][lnk-azureiotsuite] usando le credenziali dell'account Azure e quindi fare clic su **+** per creare una soluzione.
2. Fare clic su **Seleziona** nel riquadro **Remote monitoring** (Monitoraggio remoto).
3. Immettere un valore in **Nome soluzione** per la soluzione preconfigurata di monitoraggio remoto.
4. Selezionare l'**area** e la **sottoscrizione** che si desidera usare per il provisioning della soluzione.
5. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Attendere il completamento del processo di provisioning.
1. Fare clic sul riquadro della soluzione con stato **Provisioning**.
2. Notare gli stati **Provisioning** man mano che i servizi di Azure vengono distribuiti nella sottoscrizione di Azure.
3. Al termine del provisioning, lo stato cambierà in **Pronto**.
4. Fare clic sul riquadro per visualizzare i dettagli della soluzione nel riquadro di destra.

> [!NOTE]
> In caso di problemi di distribuzione della soluzione preconfigurata, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions] e le [domande frequenti][lnk-faq]. Se i problemi persistono, creare un ticket di servizio nel [portale][lnk-portal].
> 
> 

Se ci sono dettagli importanti non elencati per la soluzione, è possibile inviare suggerimenti sulle funzionalità usando i [suggerimenti degli utenti](https://feedback.azure.com/forums/321918-azure-iot).

[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-faq]: ../articles/iot-suite/iot-suite-v1-faq.md