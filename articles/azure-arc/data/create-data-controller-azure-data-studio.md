---
title: Crea controller dati in Azure Data Studio
description: Crea controller dati in Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71678977f899b910a97dbb552233f36ca5a51f26
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939999"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Crea controller dati in Azure Data Studio

È possibile creare un controller dati utilizzando Azure Data Studio tramite la distribuzione guidata e i notebook.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

- È necessario accedere a un cluster Kubernetes e fare in modo che il file kubeconfig sia configurato in modo da puntare al cluster Kubernetes in cui si vuole eseguire la distribuzione.
- È necessario [installare gli strumenti client](install-client-tools.md) , tra **cui Azure Data Studio** le estensioni Azure Data Studio denominate **Azure Arc** e l'interfaccia della riga di comando di **Azure Data**.
- È necessario accedere ad Azure in Azure Data Studio.  A tale scopo, digitare CTRL + comando + MAIUSC + P per aprire la finestra del testo del comando e digitare **Azure**.  Scegliere **Azure: accedi**.   Nel pannello che viene visualizzato fare clic sull'icona + in alto a destra per aggiungere un account Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Usare la distribuzione guidata per creare il controller dati di Azure Arc

Seguire questa procedura per creare un controller di dati di Azure ARC usando la distribuzione guidata.

1. In Azure Data Studio fare clic sulla scheda connessioni nel pannello di navigazione sinistro.
2. Fare clic sul pulsante **...** nella parte superiore del pannello connessioni e scegliere **nuova distribuzione...**
3. Nella creazione guidata nuova distribuzione scegliere **controller dati di Azure Arc**, selezionare la casella di controllo accettazione della licenza, quindi fare clic sul pulsante **Seleziona** nella parte inferiore.
4. Usare il file kubeconfig predefinito o selezionarne un altro.  Fare clic su **Avanti**.
5. Scegliere un contesto del cluster Kubernetes. Fare clic su **Avanti**.
6. Scegliere un file del profilo di configurazione della distribuzione a seconda del cluster Kubernetes di destinazione. **Fare clic su Avanti**.
8. Scegliere la sottoscrizione e il gruppo di risorse desiderati.
9. Immettere un nome per il controller dati e per lo spazio dei nomi in cui verrà creato il controller dati.  

> [!NOTE]
> Se lo spazio dei nomi esiste già, verrà usato se lo spazio dei nomi non contiene già altri oggetti Kubernetes-pod e così via.  Se lo spazio dei nomi non esiste, verrà eseguito un tentativo di creare lo spazio dei nomi.  Per creare uno spazio dei nomi in un cluster Kubernetes sono necessari i privilegi di amministratore del cluster Kubernetes.  Se non si dispone dei privilegi di amministratore del cluster Kubernetes, chiedere all'amministratore del cluster Kubernetes di eseguire i primi passaggi dell'articolo [creare un controller di dati usando gli strumenti nativi di Kubernetes](./create-data-controller-using-k8s-native-tools.md) che devono essere eseguiti da un amministratore di Kubernetes prima di completare la procedura guidata.

> [!NOTE]
> Nota: il controller dati e il nome dello spazio dei nomi verranno usati per creare una risorsa personalizzata nel cluster Kubernetes, in modo che siano conformi alle [convenzioni di denominazione di Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Selezionare una località di Azure.
   
> [!NOTE]
> Il percorso di Azure selezionato qui è il percorso in Azure in cui verranno archiviati i *metadati* sul controller dati e le istanze di database che gestisce.  Il controller di dati e le istanze del database verranno effettivamente crewted nel cluster Kubernetes, laddove possibile.

11.  Immettere un nome utente e una password e confermare la password per l'account utente dell'amministratore del controller di dati.

> [!NOTE]
> La password deve essere di almeno 8 caratteri.

1.  Fare clic su **Avanti**.
2.  Esaminare e fare clic su **script per notebook**.
3.  **Esaminare il notebook generato**.  Apportare le modifiche necessarie, ad esempio i nomi delle classi di archiviazione o i tipi di servizio.
4.  Fare clic su **Esegui tutto** nella parte superiore del notebook.

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome "Arc".  Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire ' Arc ' con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).
