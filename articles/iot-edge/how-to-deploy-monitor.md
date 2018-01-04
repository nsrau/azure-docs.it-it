---
title: Distribuire e monitorare i moduli per Azure IoT Edge | Microsoft Docs
description: Gestire i moduli eseguiti su dispositivi perimetrali
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cc7d1e290465d9254cbd7fe9e8ba71cc740b0368
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Distribuire e monitorare i moduli di IoT Edge su larga scala - Anteprima

Azure IoT Edge consente di spostare le attività di analisi sul perimetro e offre un'interfaccia cloud per gestire e monitorare i dispositivi IoT Edge senza dover accedere fisicamente a ciascuno di essi. La possibilità di gestire in remoto i dispositivi è sempre più importante con il progressivo diffondersi di soluzioni IoT (Internet delle cose) sempre più estese e complesse. Azure IoT Edge è progettato per supportare gli obiettivi aziendali, indipendentemente dal numero di dispositivi aggiunti.

È possibile gestire singoli dispositivi e distribuire i moduli in tali dispositivi uno alla volta. Tuttavia, se si vogliono apportate modifiche su larga scala ai dispositivi, è possibile creare una **distribuzione IoT Edge**. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli in più dispositivi contemporaneamente, di tenere traccia dello stato e dell'integrità dei moduli, nonché di apportare modifiche all'occorrenza. 

## <a name="identify-devices-using-tags"></a>Identificare i dispositivi tramite tag

Prima di poter creare una distribuzione, è necessario essere in grado di specificare i dispositivi a cui la si vuole applicare. Azure IoT Edge identifica i dispositivi tramite **tag** nel dispositivo gemello. Ogni dispositivo può avere più tag ed è possibile definirli in qualsiasi modo risulti appropriato per una soluzione specifica. Ad esempio, se si gestisce un complesso di edifici intelligenti, si potrebbero aggiungere i tag seguenti a un dispositivo:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT][lnk-device-twin].

## <a name="create-a-deployment"></a>Creare una distribuzione

1. Nel [portale di Azure][lnk-portal], visitare l'hub IoT. 
1. Selezionare **IoT Edge (preview)** (IoT Edge - anteprima).
1. Selezionare **Add IoT Edge device** (Aggiungi il dispositivo di IoT Edge).

La creazione di una distribuzione prevede cinque passaggi, illustrati nelle sezioni seguenti. 

### <a name="step-1-name-and-label"></a>Passaggio 1: Nome ed etichetta

1. Assegnare un nome univoco alla distribuzione. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`.
1. Aggiungere etichette per tenere traccia delle distribuzioni. Le etichette sono coppie di **Nome** e **Valore** che descrivono la distribuzione. Ad esempio, `HostPlatform, Linux` o `Version, 3.0.1`.
1. Selezionare **Avanti** per procedere al passaggio due. 

### <a name="step-2-add-modules-optional"></a>Passaggio 2: Aggiungere moduli (facoltativo)

Esistono due tipi di moduli che è possibile aggiungere a una distribuzione. Il primo è un modulo basato su un servizio di Azure, ad esempio un account di archiviazione o Analisi di flusso. Il secondo è un modulo basato su codice personalizzato. È possibile aggiungere più moduli di entrambi i tipi a una distribuzione. 

Se si crea una distribuzione senza moduli, tutti i moduli esistenti vengono rimossi dai dispositivi. 

>[!NOTE]
>Azure Machine Learning e Funzioni di Azure non supportano ancora la distribuzione automatica dei servizi di Azure. Usare la distribuzione di moduli personalizzati per aggiungere manualmente questi servizi alla distribuzione. 

Per aggiungere un modulo da Analitica di flusso di Azure, seguire questi passaggi:
1. Selezionare **modulo Importa Azure flusso Analitica IoT Edge**.
1. Usare i menu a discesa per selezionare le istanze del servizio Azure che si vuole distribuire.
1. Selezionare **Save** (Salva) per aggiungere il modulo alla distribuzione. 

Per aggiungere codice personalizzato come modulo o aggiungere manualmente un modulo per un servizio di Azure, seguire questa procedura:
1. Selezionare **Add IoT Edge module** (Aggiungi il modulo di IoT Edge).
1. Assegnare un nome al modulo in **Name** (Nome).
1. Per il **URI immagine** immettere l'immagine contenitore Docker per il modulo. 
1. Specificare qualsiasi **opzioni di creazione del contenitore** che deve essere passato al contenitore. Per altre informazioni, vedere [docker create][lnk-docker-create].
1. Usare il menu a discesa per selezionare **Restart policy** (Criteri di riavvio). È possibile scegliere tra le opzioni seguenti: 
   * **Always** (Sempre): il modulo viene sempre riavviato se viene arrestato per qualsiasi motivo.
   * **Never** (Mai): il modulo non viene mai riavviato se viene arrestato per qualsiasi motivo.
   * **On-failed** (In caso di errore): il modulo viene riavviato in caso di arresto anomalo, ma non se viene chiuso normalmente. 
   * **On-unhealthy** (Se non integro): il modulo viene riavviato in caso di arresto anomalo o se restituisce uno stato non integro. Ogni modulo deve implementare la funzione di stato di integrità. 
1. Utilizzare il menu a discesa per selezionare il **stato desiderato** per il modulo. È possibile scegliere tra le opzioni seguenti:
   * **Running** (In esecuzione): questa è l'opzione predefinita. Il modulo verrà avviato immediatamente dopo la distribuzione.
   * **Stopped** (Arrestato): dopo la distribuzione, il modulo resta inattivo fino a quando non viene chiamato per l'avvio dall'utente o da un altro modulo.
1. Selezionare **abilitare** se si desidera aggiungere la coppia di modulo qualsiasi tag o proprietà desiderate. 
1. Selezionare **Save** (Salva) per aggiungere il modulo alla distribuzione. 

Dopo aver configurato tutti i moduli per una distribuzione, selezionare **Avanti** per procedere al passaggio tre.

### <a name="step-3-specify-routes-optional"></a>Passaggio 3: Specificare le route (facoltativo)

Le route definiscono le modalità di comunicazione tra i moduli in una distribuzione. Specificare le eventuali route richieste per la distribuzione, quindi selezionare **Avanti** per procedere al passaggio quattro. 

### <a name="step-4-target-devices"></a>Passaggio 4: Assegnare i dispositivi di destinazione

Usare la proprietà tags dai dispositivi per selezionare i dispositivi specifici che devono ricevere la distribuzione. 

Dato che più distribuzioni potrebbero avere come destinazione lo stesso dispositivo, è necessario assegnare a ogni distribuzione un numero di priorità. In caso di conflitto, prevale la distribuzione con la priorità più alta. Se due distribuzioni hanno lo stesso numero di priorità, prevale quella creata più di recente. 

1. Immettere un numero intero positivo in **Priority** (Priorità) per la distribuzione.
1. Specificare una condizione in **Target condition** (Condizione di destinazione) per determinare i dispositivi di destinazione di questa distribuzione. La condizione è basata sui tag del dispositivo gemello e deve corrispondere al formato di espressione. Ad esempio, `tags.environment='test'`. 
1. Selezionare **Avanti** per procedere al passaggio finale.

### <a name="step-5-review-template"></a>Passaggio 5: Rivedere il modello

Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

## <a name="monitor-a-deployment"></a>Monitorare una distribuzione

Per visualizzare i dettagli di una distribuzione e monitorare i dispositivi che la eseguono, seguire questa procedura:

1. Accedere al [portale di Azure][lnk-portal] e passare all'hub IoT. 
1. Selezionare **IoT Edge (preview)** (IoT Edge - anteprima).
1. Selezionare **IoT Edge deployments** (Distribuzioni IoT Edge). 

   ![Visualizzare le distribuzioni IoT Edge][1]

1. Esaminare l'elenco delle distribuzioni. Per ogni distribuzione, è possibile visualizzare i dettagli seguenti:
   * **ID**: nome della distribuzione.
   * **Target condition** (Condizione di destinazione): tag usato per definire i dispositivi di destinazione.
   * **Priority** (Priorità): numero di priorità assegnato alla distribuzione.
   * **IoT Edge agent status** (Stato dell'agente IoT Edge): numero di dispositivi che hanno ricevuto la distribuzione e i relativi stati di integrità. 
   * **Unhealthy modules** (Moduli non integri): numero di moduli nella distribuzione che segnalano errori. 
   * **Creation time** (Data/ora di creazione): timestamp di creazione della distribuzione. Questo timestamp viene usato per stabilire la prevalenza quando due distribuzioni hanno la stessa priorità. 
1. Selezionare la distribuzione che si vuole monitorare.  
1. Esaminare i dettagli della distribuzione. È possibile usare le schede per visualizzare dettagli specifici sui dispositivi che hanno ricevuto la distribuzione: 
   * **Targeted** (Destinazione): dispositivi perimetrali che soddisfano la condizione di destinazione. 
   * **Applied** (Applicata): dispositivi perimetrali che non rappresentano la destinazione di un'altra distribuzione con priorità più alta. Questi sono i dispositivi che ricevono in effetti la distribuzione. 
   * **Reporting success** (Distribuzione riuscita): dispositivi perimetrali a cui è applicata la distribuzione, che confermano al servizio la corretta distribuzione dei moduli. 
   * **Reporting failure** (Distribuzione non riuscita): dispositivi perimetrali a cui è applicata la distribuzione, che segnalano al servizio la distribuzione non corretta di uno o più moduli. Per ulteriori indagini sull'errore, è necessario connettersi in remoto a tali dispositivi e visualizzare i file di log. 
   * **Reporting unhealthy modules** (Moduli non integri): dispositivi perimetrali a cui è applicata la distribuzione, che comunicano al servizio che uno o più moduli sono stati distribuiti correttamente ma ora segnalano errori. 

## <a name="modify-a-deployment"></a>Modificare una distribuzione

Quando si modifica una distribuzione, le modifiche vengono replicate immediatamente in tutti i dispositivi di destinazione. 

Se si aggiorna la condizione di destinazione, vengono eseguiti gli aggiornamenti seguenti:
* Se un dispositivo non soddisfa la condizione di destinazione precedente, ma soddisfa la nuova condizione di destinazione e questa distribuzione ha la priorità più alta per il dispositivo, la distribuzione viene applicata al dispositivo. 
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione, disinstalla questa distribuzione e riceve la distribuzione successiva nell'ordine di priorità. 
* Se un dispositivo che esegue la distribuzione non soddisfa più la condizione di destinazione e non soddisfa la condizione di destinazione di tutte le altre distribuzioni, nel dispositivo non viene apportata alcuna modifica. Il dispositivo continua a eseguire i moduli corrente nello stato corrente, ma non è più gestito come parte di questa distribuzione. Quando soddisfa la condizione di destinazione di qualsiasi altra distribuzione, disinstalla questa distribuzione e riceve quella nuova. 

Per modificare una distribuzione, seguire questa procedura: 

1. Accedere al [portale di Azure][lnk-portal] e passare all'hub IoT. 
1. Selezionare **IoT Edge (preview)** (IoT Edge - anteprima).
1. Selezionare **IoT Edge deployments** (Distribuzioni IoT Edge). 

   ![Visualizzare le distribuzioni IoT Edge][1]

1. Selezionare la distribuzione che si vuole modificare. 
1. Apportare modifiche nei campi seguenti: 
   * Condizione di destinazione 
   * Etichette 
   * Priorità 
1. Selezionare **Salva**.
1. Seguire i passaggi in [Monitorare una distribuzione][anchor-monitor] per controllare la distribuzione delle modifiche. 

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Quando si elimina una distribuzione, tutti i dispositivi ricevono la distribuzione successiva nell'ordine di priorità. Se i dispositivi non soddisfano la condizione di destinazione di qualsiasi altra distribuzione, i moduli non vengono rimossi in seguito all'eliminazione della distribuzione. 

1. Accedere al [portale di Azure][lnk-portal] e passare all'hub IoT. 
1. Selezionare **IoT Edge (preview)** (IoT Edge - anteprima).
1. Selezionare **IoT Edge deployments** (Distribuzioni IoT Edge). 

   ![Visualizzare le distribuzioni IoT Edge][1]

1. Usare la casella di controllo per selezionare la distribuzione che si vuole eliminare. 
1. Selezionare **Elimina**.
1. Un messaggio indicherà che questa azione comporta l'eliminazione della distribuzione e il ripristino dello stato precedente per tutti i dispositivi.  Ciò significa che verrà applicata una distribuzione con una priorità più bassa.  Se non viene assegnata alcun'altra distribuzione, i moduli non verranno rimossi. Se i clienti desiderano procedere in questo modo, devono creare una distribuzione con zero moduli e distribuirla agli stessi dispositivi. Selezionare **Yes** (Sì) per continuare. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli nei dispositivi perimetrali][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
