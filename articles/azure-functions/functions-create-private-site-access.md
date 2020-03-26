---
title: Abilitare l'accesso privato al sito per Funzioni di Azure
description: Informazioni su come configurare l'accesso privato al sito con una rete virtuale di Azure per Funzioni di Azure.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78852016"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Esercitazione: Stabilire l'accesso privato al sito per Funzioni di Azure

Questa esercitazione illustra come abilitare l'[accesso privato al sito](./functions-networking-options.md#private-site-access) con Funzioni di Azure. Usando l'accesso privato al sito, è possibile richiedere che il codice della funzione venga attivato solo da una rete virtuale specifica.

L'accesso privato al sito è utile negli scenari in cui l'accesso all'app per le funzioni deve essere limitato a una rete virtuale specifica. L'app per le funzioni potrebbe essere applicabile, ad esempio, solo ai dipendenti di un'organizzazione specifica o ai servizi inclusi nella rete virtuale specificata, come un'altra funzione di Azure, una macchina virtuale di Azure o un cluster del servizio Azure Kubernetes.

Se un'app per le funzioni deve accedere a risorse di Azure nella rete virtuale o connesse tramite [endpoint servizio](../virtual-network/virtual-network-service-endpoints-overview.md), è necessaria l'[integrazione con la rete virtuale](./functions-create-vnet.md).

Questa esercitazione illustra come configurare l'accesso privato al sito per l'app per le funzioni:

> [!div class="checklist"]
> * Creare una macchina virtuale
> * Creare un servizio Azure Bastion
> * Creare un'app Funzioni di Azure
> * Configurare un endpoint servizio di rete virtuale
> * Creare e distribuire una funzione di Azure
> * Richiamare la funzione dall'esterno e dall'interno della rete virtuale

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="topology"></a>Topologia

Il diagramma seguente illustra l'architettura della soluzione da creare:

![Diagramma dell'architettura generale della soluzione di accesso privato al sito](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è importante comprendere l'indirizzamento IP e la creazione di subnet. Si può iniziare con [questo articolo che illustra le nozioni di base dell'indirizzamento e della creazione di subnet](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Molti altri articoli e video sono disponibili online.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Il primo passaggio di questa esercitazione consiste nel creare una nuova macchina virtuale all'interno di una rete virtuale.  La macchina virtuale verrà usata per accedere alla funzione dopo che l'accesso è stato limitato in modo che sia disponibile solo dalla rete virtuale.

1. Selezionare il pulsante **Crea una risorsa**.

2. Nel campo di ricerca digitare `Windows Server` e selezionare **Windows Server** nei risultati della ricerca.

3. Selezionare **Windows Server 2019 Datacenter** nell'elenco delle opzioni Windows Server e fare clic sul pulsante **Crea**.

4. Nella scheda **Informazioni di base** usare le impostazioni della VM specificate nella tabella sotto l'immagine:

    >[!div class="mx-imgBorder"]
    >![Scheda Informazioni di base per una nuova VM Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. |
    | [**Gruppo di risorse**](../azure-resource-manager/management/overview.md) | myResourceGroup | Scegliere il gruppo di risorse che conterrà tutte le risorse per questa esercitazione.  L'uso dello stesso gruppo di risorse semplifica la pulizia delle risorse al termine dell'esercitazione. |
    | **Nome macchina virtuale** | myVM | Il nome della VM deve essere univoco nel gruppo di risorse. |
    | [**Area**](https://azure.microsoft.com/regions/) | (Stati Uniti) Stati Uniti centro-settentrionali | Scegliere un'area nelle vicinanze o vicina alle funzioni a cui si dovrà accedere. |
    | **Porte in ingresso pubbliche** | nessuno | Selezionare **Nessuna** affinché non sia presente connettività in ingresso da Internet alla VM. L'accesso remoto alla VM verrà configurato tramite il servizio Azure Bastion. |

5. Scegliere la scheda **Rete** e selezionare **Crea nuova** per configurare una nuova rete virtuale.

    >[!div class="mx-imgBorder"]
    >![Creare una nuova rete virtuale per la nuova VM](./media/functions-create-private-site-access/create-vm-networking.png)

6. In **Crea rete virtuale** usare le impostazioni della tabella sotto l'immagine:

    >[!div class="mx-imgBorder"]
    >![Creare una nuova rete virtuale per la nuova VM](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Si può usare il nome predefinito generato per la rete virtuale. |
    | **Intervallo di indirizzi** | 10.10.0.0/16 | Usare un singolo intervallo di indirizzi per la rete virtuale. |
    | **Nome della subnet** | Esercitazione | Nome della subnet. |
    | **Intervallo di indirizzi** (subnet) | 10.10.1.0/24 | Le dimensioni della subnet definiscono il numero di interfacce che è possibile aggiungere alla subnet. Questa subnet viene usata dalla VM. Una subnet `/24` offre 254 indirizzi host. |

7. Selezionare **OK** per creare la rete virtuale.
8. Nella scheda **Rete** assicurarsi che per **IP pubblico** sia selezionata l'opzione **Nessuno**.
9. Scegliere la scheda **Gestione** e quindi **Crea nuovo** in **Account di archiviazione di diagnostica** per creare un nuovo account di archiviazione.
10. Lasciare i valori predefiniti per le sezioni **Identità**, **Arresto automatico** e **Backup**.
11. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**. Il processo di creazione della VM richiederà alcuni minuti.

## <a name="configure-azure-bastion"></a>Configurare Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) è un servizio di Azure completamente gestito che offre un accesso RDP e SSH sicuro alle macchine virtuali direttamente dal portale di Azure. L'uso del servizio Azure Bastion elimina l'esigenza di configurare le impostazioni di rete correlate all'accesso RDP.

1. Nel portale scegliere **Aggiungi** nella parte superiore della visualizzazione del gruppo di risorse.
2. Nel campo di ricerca digitare "Bastion".  Selezionare "Bastion".
3. Selezionare **Crea** per iniziare il processo di creazione di una nuova risorsa Azure Bastion. Nella sezione **Rete virtuale** verrà visualizzato un messaggio di errore perché non è ancora presente una subnet `AzureBastionSubnet`. La subnet verrà creata nei passaggi seguenti. Usare le impostazioni della tabella sotto l'immagine:

    >[!div class="mx-imgBorder"]
    >![Avvio della creazione di un'istanza di Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myBastion | Nome della nuova risorsa Bastion. |
    | **Area** | Stati Uniti centro-settentrionali | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |
    | **Rete virtuale** | myResourceGroup-vnet | Rete virtuale in cui verrà creata la risorsa Bastion. |
    | **Subnet** | AzureBastionSubnet | Subnet della rete virtuale in cui verrà distribuita la nuova risorsa host Bastion. È necessario creare una subnet usando il nome `AzureBastionSubnet`. Questo valore indica ad Azure la subnet in cui devono essere distribuite le risorse Bastion. È necessario usare una subnet almeno `/27` o di dimensioni superiori (`/27`, `/26`e così via). |

    > [!NOTE]
    > Per una guida dettagliata per la creazione di una risorsa Azure Bastion, vedere l'esercitazione [Creare un host Azure Bastion](../bastion/bastion-create-host-portal.md).

4. Creare una subnet in cui Azure potrà effettuare il provisioning dell'host Azure Bastion. Scegliendo **Gestisci configurazione subnet** verrà visualizzato un nuovo riquadro in cui è possibile definire una nuova subnet.  Scegliere **+ Subnet** per creare una nuova subnet.
5. La subnet deve essere denominata `AzureBastionSubnet` e il prefisso della subnet deve essere almeno `/27`.  Selezionare **OK** per creare la subnet.

    >[!div class="mx-imgBorder"]
    >![Creare la subnet per l'host Azure Bastion](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Nella pagina **Crea un'istanza di Bastion** selezionare la subnet `AzureBastionSubnet` appena creata nell'elenco delle subnet disponibili.

    >[!div class="mx-imgBorder"]
    >![Creare un host Azure Bastion con una subnet specifica](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**. La creazione della risorsa Azure Bastion richiederà alcuni minuti.

## <a name="create-an-azure-functions-app"></a>Creare un'app Funzioni di Azure

Il passaggio successivo consiste nel creare un'app per le funzioni in Azure usando il [piano a consumo](functions-scale.md#consumption-plan). Più avanti nell'esercitazione si distribuirà il codice della funzione in questa risorsa.

1. Nel portale scegliere **Aggiungi** nella parte superiore della visualizzazione del gruppo di risorse.
2. Selezionare **Calcolo > App per le funzioni**.
3. Nella sezione **Informazioni di base** usare le impostazioni dell'app per le funzioni specificate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Gruppo di risorse** | myResourceGroup | Scegliere il gruppo di risorse che conterrà tutte le risorse per questa esercitazione.  L'uso dello stesso gruppo di risorse per l'app per le funzioni e la VM semplifica la pulizia delle risorse al termine dell'esercitazione. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono a-z (senza distinzione tra maiuscole e minuscole), 0-9 e -. |
    | **Pubblica** | Codice | Opzione per la pubblicazione di file di codice o di un contenitore Docker. |
    | **Stack di runtime** | Lingua preferita | Scegliere un runtime che supporti il linguaggio di programmazione della funzione preferito. |
    | **Area** | Stati Uniti centro-settentrionali | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    Fare clic sul pulsante **Avanti: Hosting >** .
4. Nella sezione **Hosting** selezionare le impostazioni corrette per **Account di archiviazione**, **Sistema operativo** e **Piano**, come descritto nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Account di archiviazione** | Nome globalmente univoco | Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](./functions-scale.md#storage-account-requirements). |
    | **Sistema operativo** | Sistema operativo preferito | Viene preselezionato automaticamente un sistema operativo in base alla selezione dello stack di runtime, ma, se necessario, è possibile modificare l'impostazione. |
    | **Pianificare** | Consumo | Il [piano di hosting](./functions-scale.md) determina la modalità di scalabilità dell'app per le funzioni e la disponibilità delle risorse per ogni istanza. |
5. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.
6. Selezionare **Crea** per effettuare il provisioning dell'app per le funzioni e distribuirla.

## <a name="configure-access-restrictions"></a>Configurare le restrizioni di accesso

Il passaggio successivo consiste nel configurare le [restrizioni di accesso](../app-service/app-service-ip-restrictions.md) affinché la funzione possa essere richiamata solo dalle risorse nella rete virtuale.

L'[accesso privato al sito](functions-networking-options.md#private-site-access) viene abilitato creando un [endpoint servizio](../virtual-network/virtual-network-service-endpoints-overview.md) di rete virtuale di Azure tra l'app per le funzioni e la rete virtuale specificata. Le restrizioni di accesso vengono implementate tramite endpoint servizio, che fanno sì che alla risorsa designata possa accedere solo il traffico proveniente dalla rete virtuale specificata. In questo caso, la risorsa designata è la funzione di Azure.

1. All'interno dell'app per le funzioni passare alla scheda **Funzionalità della piattaforma**. Selezionare il collegamento **Rete** sotto l'intestazione della sezione *Rete* per aprire la sezione Stato funzionalità di rete.
2. La pagina **Stato funzionalità di rete** è il punto di partenza per configurare Frontdoor di Azure, Rete CDN di Azure nonché le restrizioni di accesso. Selezionare **Configura restrizioni di accesso** per configurare l'accesso privato al sito.
3. Nella pagina **Restrizioni di accesso** risulta presente solo la restrizione predefinita. L'impostazione predefinita non prevede alcuna restrizione per l'accesso all'app per le funzioni.  Selezionare **Aggiungi regola** per creare la configurazione di una restrizione per l'accesso privato al sito.
4. Nel riquadro **Aggiungi restrizione di accesso** selezionare **Rete virtuale** nella casella di riepilogo a discesa **Tipo** e quindi la rete virtuale e la subnet create in precedenza.
5. Nella pagina **Restrizioni di accesso** verrà ora visualizzata una nuova restrizione. Il cambiamento di **Stato endpoint** da `Disabled` a `Provisioning` e quindi `Enabled` potrebbe richiedere alcuni secondi.

    >[!IMPORTANT]
    > Ogni app per le funzioni ha un [sito Strumenti avanzati (Kudu)](../app-service/app-service-ip-restrictions.md#scm-site) che viene usato per gestire le distribuzioni dell'app per le funzioni. Questo sito è accessibile da un URL come `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Dato che in questo sito di distribuzione non sono state abilitate le restrizioni di accesso, è ancora possibile distribuire il codice del progetto da una workstation di sviluppo locale o da un servizio di compilazione senza dover effettuare il provisioning di un agente nella rete virtuale.

## <a name="access-the-functions-app"></a>Accedere all'app per le funzioni

1. Tornare all'app per le funzioni creata in precedenza.  Nella sezione **Panoramica** copiare l'URL.

    >[!div class="mx-imgBorder"]
    >![Recuperare l'URL dell'app per le funzioni](./media/functions-create-private-site-access/access-function-overview.png)

2. Se ora si prova ad accedere all'app per le funzioni da un computer all'esterno della rete virtuale, verrà visualizzata una pagina HTTP 403 che indica che l'app è stata arrestata.  L'app non è stata arrestata. La risposta è in realtà uno stato HTTP 403 per IP non consentito.
3. Accedere quindi alla funzione dalla macchina virtuale creata in precedenza, connessa alla rete virtuale. Per accedere al sito dalla VM sarà necessario connettersi alla VM tramite il servizio Azure Bastion.  Selezionare prima di tutto **Connetti** e quindi scegliere **Bastion**.
4. Specificare il nome utente e la password necessari per accedere alla macchina virtuale.  Selezionare **Connetti**. Verrà visualizzata una nuova finestra del browser che consente di interagire con la macchina virtuale.
5. Dato che la VM accede alla funzione tramite la rete virtuale, è possibile accedere al sito dal Web browser nella VM.  È importante notare che, anche se l'app per le funzioni è accessibile solo dalla rete virtuale designata, è ancora presente una voce DNS pubblica. Come illustrato in precedenza, il tentativo di accedere al sito genererà una risposta HTTP 403.

## <a name="create-a-function"></a>Creare una funzione

Il passaggio successivo di questa esercitazione consiste nel creare una funzione di Azure attivata da HTTP. La chiamata della funzione tramite HTTP GET o POST genererà una risposta "Hello, {nome}".  

1. Seguire una di queste guide di avvio rapido per creare e distribuire l'app Funzioni di Azure:

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Riga di comando](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Quando si pubblica il progetto Funzioni di Azure, scegliere la risorsa app per le funzioni creata in precedenza in questa esercitazione.
3. Verificare che la funzione sia stata distribuita.

    >[!div class="mx-imgBorder"]
    >![Funzione distribuita nell'elenco delle funzioni](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Richiamare direttamente la funzione

1. Per testare l'accesso alla funzione, è necessario copiare l'URL della funzione. Selezionare la funzione distribuita e quindi **</> Recupera URL della funzione**. Fare quindi clic sul pulsante **Copia** per copiare l'URL negli Appunti.

    >[!div class="mx-imgBorder"]
    >![Copiare l'URL della funzione](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Quando la funzione viene eseguita, nel portale verrà visualizzato un errore di runtime che indica che non è possibile avviare il runtime della funzione. Nonostante il testo del messaggio, in realtà l'app per le funzioni è in esecuzione. L'errore è causato dalle nuove restrizioni di accesso, che impediscono al portale di eseguire query per controllare il runtime.

2. Incollare l'URL in un Web browser. Provando ora ad accedere all'app per le funzioni da un computer all'esterno della rete virtuale verrà visualizzata una risposta HTTP 403 che indica che l'app è stata arrestata.

## <a name="invoke-the-function-from-the-virtual-network"></a>Richiamare la funzione dalla rete virtuale

L'accesso alla funzione tramite un Web browser nella VM configurata nella rete virtuale, usando il servizio Azure Bastion, avrà esito positivo.

>[!div class="mx-imgBorder"]
>![Accedere alla funzione di Azure tramite Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi


> [!div class="nextstepaction"]
> [Altre informazioni sulle opzioni di rete in Funzioni](./functions-networking-options.md)
