---
title: Connettere un computer ibrido con server abilitati per Azure Arc
description: Informazioni su come connettere e registrare un computer ibrido con server abilitati per Azure Arc.
ms.topic: quickstart
ms.date: 11/12/2020
ms.openlocfilehash: 3779d95ac138e83b1d953f744e07ae553890a5d7
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576841"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Avvio rapido: Connettere un computer ibrido con server abilitati per Azure Arc

I [server abilitati per Azure Arc](../overview.md) consentono di gestire e regolamentare i computer Windows e Linux ospitati in locale, al perimetro e in ambienti multicloud. In questa guida di avvio rapido verrà distribuito e configurato l'agente Connected Machine nel computer Windows o Linux ospitato all'esterno di Azure per la gestione tramite server abilitati per Arc.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Per distribuire l'agente Connected Machine ibrido in server abilitati per Arc, è necessario avere autorizzazioni di amministratore nel computer in cui installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, con un account membro del gruppo Administrators locale.

* Prima di iniziare, assicurarsi di controllare i [prerequisiti](../agent-overview.md#prerequisites) dell'agente e di verificare quanto segue:

    * La macchina di destinazione deve eseguire un [sistema operativo](../agent-overview.md#supported-operating-systems) supportato.

    * All'account deve essere stata concessa l'assegnazione ai [ruoli di Azure richiesti](../agent-overview.md#required-permissions).

    * Se la macchina si connette tramite un firewall o un server proxy per comunicare su Internet, verificare che gli URL [elencati](../agent-overview.md#networking-configuration) non siano bloccati.

    * I server abilitati per Azure Arc supportano solo le aree specificate [qui](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrare i provider di risorse di Azure

I server abilitati per Azure Arc dipendono dai provider di risorse di Azure seguenti nella sottoscrizione per poter usare questo servizio:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Registrarli con questi comandi:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generare lo script di installazione

Lo script per automatizzare il download, l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Avviare il servizio Azure Arc nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Server - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

1. In alto a sinistra nella pagina **Server - Azure Arc** selezionare **Aggiungi**.

1. Nella pagina **Selezionare un metodo** selezionare il riquadro **Aggiungi server tramite script interattivo** e quindi **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale. La località può essere uguale o diversa da quella del gruppo di risorse.

1. Nella pagina **Prerequisiti** esaminare le informazioni e quindi selezionare **Avanti: Dettagli risorsa**.

1. Nella pagina **Dettagli risorsa** specificare quanto segue:

    1. Nell'elenco a discesa **Gruppo di risorse** selezionare il gruppo di risorse da cui verrà gestito il computer.
    1. Nell'elenco a discesa **Area** selezionare l'area di Azure in cui archiviare i metadati dei server.
    1. Nell'elenco a discesa **Sistema operativo** selezionare il sistema operativo in cui verrà eseguito lo script.
    1. Se il computer comunica tramite un server proxy per connettersi a Internet, specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer userà per le comunicazioni. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`.
    1. Selezionare **Avanti: Tag**.

1. Nella pagina **Tag** esaminare l'impostazione predefinita e suggerita di **Tag di località fisica** e immettere un valore oppure specificare una o più opzioni per **Tag personalizzati** in base ai propri standard.

1. Selezionare **Avanti: Scarica ed esegui script**.

1. Nella pagina **Scarica ed esegui script** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

## <a name="install-the-agent-using-the-script"></a>Installare l'agente con lo script

### <a name="windows-agent"></a>Agente Windows

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati a 64 bit.

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.ps1`.

### <a name="linux-agent"></a>Agente Linux

1. Per installare l'agente Linux nella macchina di destinazione in grado di comunicare direttamente con Azure, eseguire il comando seguente:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Se la macchina di destinazione comunica tramite un server proxy, eseguire il comando seguente:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare la propria macchina virtuale nel [portale di Azure](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Connessione alla macchina riuscita" border="false":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver abilitato la macchina virtuale ibrida Linux o Windows e averla connessa al servizio, si è pronti per abilitare Criteri di Azure per comprendere la conformità in Azure.

Per informazioni su come identificare un computer abilitato con server abilitati per Azure Arc in cui non è installato l'agente di Log Analytics, continuare con l'esercitazione:

> [!div class="nextstepaction"]
> [Creare un'assegnazione di criteri per identificare le risorse non conformi](tutorial-assign-policy-portal.md)
