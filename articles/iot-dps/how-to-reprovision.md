---
title: Come rieffettuare il provisioning dei dispositivi nel servizio Device Provisioning in hub IoT di Azure | Documentazione Microsoft
description: Come rieffettuare il provisioning dei dispositivi in un'istanza del servizio Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799134"
---
# <a name="how-to-reprovision-devices"></a>Come rieffettuare il provisioning dei dispositivi

Durante il ciclo di vita di una soluzione IoT, è comune spostare i dispositivi tra hub IoT. Le ragioni di questo spostamento possono includere gli scenari seguenti:

* **Georilevazione**: Quando un dispositivo si sposta tra le posizioni, latenza di rete è stata migliorata facendo in modo che il dispositivo di eseguire la migrazione a un hub IoT più vicini a ogni posizione.

* **Multi-tenancy**: Un dispositivo è stato utilizzato all'interno della stessa soluzione IoT ma, riassegnato o assegnato in lease a un nuovo cliente, o di un sito cliente. Questo nuovo cliente può essere servito usando un hub IoT diverso.

* **Modifica di soluzione**: Un dispositivo potrebbe essere spostato in una soluzione IoT nuova o aggiornata. Questa riassegnazione potrebbe richiedere al dispositivo di comunicare con un nuovo hub IoT collegato ad altri componenti di back-end. 

* **Quarantine**: Simile a una modifica della soluzione. Un dispositivo malfunzionante, compromesso o obsoleto può essere riassegnato a un hub IoT in cui tutto ciò che può fare è aggiornare e ripristinare la conformità. Una volta che il dispositivo funziona correttamente, viene quindi migrato nuovamente al suo hub principale.

Per più di una panoramica più dettagliata di un nuovo provisioning, vedere [dispositivo Hub IoT un nuovo provisioning concetti](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configurare i criteri di allocazione di registrazione

I criteri di allocazione determinano il modo in cui i dispositivi associati con la registrazione saranno allocati o assegnati a un hub IoT una volta rieffettuato il provisioning.

I passaggi seguenti consentono di configurare i criteri di allocazione per la registrazione di un dispositivo:

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'istanza del servizio Device Provisioning.

2. Fare clic su **Gestisci registrazioni**, quindi sul gruppo di registrazione o sulla registrazione singola che si desidera configurare per rieffettuare il provisioning. 

3. Alla voce **Seleziona come assegnare i dispositivi agli hub**, selezionare uno dei criteri di allocazione seguenti:

    * **Latenza più bassa**: Questo criterio assegna i dispositivi all'IoT Hub collegato che genererà le comunicazioni di latenza più basse tra dispositivo e l'IoT Hub. Questa opzione consente al dispositivo di comunicare con l'hub IoT più vicino in base alla posizione. 
    
    * **Distribuzione ponderata uniforme**: Questo criterio distribuisce i dispositivi tra l'hub IoT collegati in base al peso allocazione assegnato a ogni hub IoT collegato. Questo criterio consente di bilanciare il carico dei dispositivi in un gruppo di hub collegati in base ai pesi di allocazione assegnati a tali hub. Se si effettua il provisioning dei dispositivi in un solo hub IoT, è consigliabile utilizzare questa impostazione. Questa è l'impostazione predefinita. 
    
    * **Configurazione statica**: Questo criterio richiede un IoT Hub desiderato elencati nella voce di registrazione per effettuarne il provisioning del dispositivo. Questo criterio consente di designare un singolo hub IoT specifico a cui assegnare i dispositivi.

4. Alla voce **Seleziona gli hub IoT a cui questo gruppo può essere assegnato**, selezionare gli hub IoT collegati da includere con i criteri di allocazione. Facoltativamente, aggiungere un nuovo hub IoT collegato usando il pulsante **Collega un nuovo hub IoT**.

    Con il criterio di allocazione **Latenza più bassa** gli hub selezionati verranno inclusi durante la valutazione di latenza per determinare l'hub più vicino a cui assegnare il dispositivo.

    Con il criterio di allocazione **Distribuzione ponderata uniforme** i dispositivi avranno un carico bilanciato in tutti gli hub selezionati sulla base dei pesi di allocazione configurati e del carico corrente del dispositivo.

    Con il criterio di allocazione **Configurazione statica** si seleziona l'hub IoT a cui assegnare i dispositivi.

4. Fare clic su **Salva** o procedere alla sezione successiva per impostare i criteri per rieffettuare il provisioning.

    ![Selezionare i criteri di allocazione della registrazione](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Impostare i criteri per rieffettuare il provisioning

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'istanza del servizio Device Provisioning.

2. Fare clic su **Gestisci registrazioni**, quindi sul gruppo di registrazione o sulla registrazione singola che si desidera configurare per rieffettuare il provisioning.

3. Alla voce **Seleziona come gestire i dati del dispositivo durante il provisioning a un hub diverso**, scegliere uno dei seguenti criteri per rieffettuare il provisioning:

    * **Rieseguire il provisioning e la migrazione dei dati**: Questo criterio interviene quando associati alla voce di registrazione dispositivi di inviano una nuova richiesta di provisioning. A seconda della configurazione della voce di registrazione, il dispositivo può essere riassegnato a un altro hub IoT. Se il dispositivo sta cambiando hub IoT, la registrazione del dispositivo con l'hub IoT iniziale verrà rimossa. Tutte le informazioni sullo stato del dispositivo da tale hub IoT iniziale verranno migrate sul nuovo hub IoT. Durante la migrazione, lo stato del dispositivo risulterà come **In fase di assegnazione**

    * **Rieseguire il provisioning e reimpostare la configurazione iniziale**: Questo criterio interviene quando associati alla voce di registrazione dispositivi di inviano una nuova richiesta di provisioning. A seconda della configurazione della voce di registrazione, il dispositivo può essere riassegnato a un altro hub IoT. Se il dispositivo sta cambiando hub IoT, la registrazione del dispositivo con l'hub IoT iniziale verrà rimossa. I dati di configurazione iniziali che l'istanza del servizio di provisioning ha ricevuto durante il provisioning del dispositivo vengono forniti al nuovo hub IoT. Durante la migrazione, lo stato del dispositivo risulterà come **In fase di assegnazione**.

4. Fare clic su **Salva** per rieffettuare il provisioning del dispositivo in base alle modifiche apportate.

    ![Selezionare i criteri di allocazione della registrazione](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Inviare una richiesta di provisioning dal dispositivo

Affinché si possa rieffettuare il provisioning dei dispositivi in base alle modifiche alla configurazione apportate nelle sezioni precedenti, questi dispositivi devono richiedere un nuovo provisioning. 

La frequenza con cui un dispositivo invia una richiesta di provisioning dipende dallo scenario. Tuttavia, si consiglia di programmare i dispositivi in modo che inviino una richiesta di provisioning a un'istanza di servizio di provisioning al riavvio del sistema e affinché supportino un [metodo](../iot-hub/iot-hub-devguide-direct-methods.md) per attivare manualmente il provisioning su richiesta. Il provisioning può essere attivato anche impostando una [proprietà desiderata](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Il criterio per rieffettuare il provisioning su una voce di registrazione determina in che modo l'istanza del servizio di provisioning del dispositivo gestisce queste richieste di provisioning e se i dati sullo stato del dispositivo devono essere migrati durante il nuovo provisioning. Gli stessi criteri sono disponibili per le registrazioni individuali e di gruppi:

Per un esempio di codice di invio per le richieste di provisioning da un dispositivo durante una sequenza di avvio, vedere [Provisioning automatico di un dispositivo simulato](quick-create-simulated-device.md).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su altre Reprovisioning, vedere [dispositivo Hub IoT un nuovo provisioning concetti](concepts-device-reprovision.md) 
- Per informazioni su ulteriori il deprovisioning di utenti, vedere [come effettuare il deprovisioning di dispositivi che sono stati precedentemente sottoposti a provisioning](how-to-unprovision-devices.md) 











