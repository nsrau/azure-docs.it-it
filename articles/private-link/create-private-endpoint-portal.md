---
title: 'Avvio rapido: Creare un endpoint privato con il portale di Azure'
description: Questa guida di avvio rapido illustra come creare un endpoint privato con il portale di Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018070"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Avvio rapido: Creare un endpoint privato con il portale di Azure

Introduzione al collegamento privato di Azure, che consente di connettersi in modo sicuro a un'app Web di Azure tramite un endpoint privato.

In questa guida di avvio rapido si creerà un endpoint privato per un'app Web di Azure e si distribuirà una macchina virtuale per testare la connessione privata.  

È possibile creare endpoint privati per diversi tipi di servizi di Azure, ad esempio Azure SQL e Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un'app Web di Azure con un piano di servizio app **PremiumV2-tier** o superiore distribuita nella sottoscrizione di Azure.  
    * Per altre informazioni e un esempio, vedere [Avvio rapido: Creare un'app Web ASP.NET Core in Azure](../app-service/quickstart-dotnetcore.md). 
    * Per un'esercitazione dettagliata sulla creazione di un'app Web e di un endpoint, vedere [Esercitazione: Connettersi a un'app Web con un endpoint privato di Azure](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **CreatePrivateEndpointQS-rg** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Europa occidentale**.|

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **mySubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

7. Selezionare **Salva**.

8. Selezionare la scheda **Sicurezza**.

9. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Create** (Crea).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

In questa sezione si creerà una macchina virtuale che verrà usata per testare l'endpoint privato.

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale** oppure cercare **Macchina virtuale** nella casella di ricerca.
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **CreatePrivateEndpointQS-rg** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Region | Selezionare **Europa occidentale**. |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **mySubnet** |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | **Base**|
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

In questa sezione verrà creato un endpoint privato per l'app Web creata nella sezione dei prerequisiti.

1. Nell'angolo in alto a sinistra della schermata nel portale selezionare **Crea una risorsa** > **Rete** > **Collegamento privato** oppure immettere **Collegamento privato** nella casella di ricerca.

2. Selezionare **Create** (Crea).

3. Nel menu a sinistra di **Centro collegamento privato** selezionare **Endpoint privato**.

4. In **Endpoint privati** selezionare **+ Aggiungi**.

5. Nella scheda **Informazioni di base** di **Crea un endpoint privato** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **CreatePrivateEndpointQS-rg**. Questo gruppo di risorse è stato creato nella sezione precedente.|
    | **Dettagli istanza** |  |
    | Nome  | Immettere **myPrivateEndpoint**. |
    | Region | Selezionare **Europa occidentale**. |

6. Selezionare la scheda **Risorsa** o il pulsante **Avanti: Risorsa** nella parte inferiore della pagina.
    
7. In **Risorsa** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Metodo di connessione | Selezionare **Connettersi a una risorsa di Azure nella directory**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.Web/sites**. |
    | Risorsa | Selezionare **\<your-web-app-name>**. </br> Selezionare il nome dell'app Web creata nei prerequisiti. |
    | Sottorisorsa di destinazione | Selezionare **siti**. |

8. Selezionare la scheda **Configurazione** o il pulsante **Avanti: Configurazione** nella parte inferiore della pagina.

9. In **Configurazione** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Rete** |  |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet**. |
    | **Integrazione DNS privato** |  |
    | Integra con la zona DNS privato | Lasciare l'impostazione predefinita **Sì**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Zone DNS privato | Lasciare l'impostazione predefinita **(Nuovo) privatelink.azurewebsites.net**.
    

13. Selezionare **Rivedi e crea**.

14. Selezionare **Create** (Crea).

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi all'app Web tramite l'endpoint privato.

1. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

2. Selezionare **CreatePrivateEndpointQS-rg**.

3. Selezionare **myVM**.

4. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

7. Aprire Windows PowerShell nel server dopo la connessione.

8. Immettere `nslookup <your-webapp-name>.azurewebsites.net`. Sostituire **\<your-webapp-name>** con il nome dell'app Web creata nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Per il nome dell'app Web viene restituito l'indirizzo IP privato **10.1.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.

11. Nella connessione bastion a **myVM** aprire Internet Explorer.

12. Immettere l'URL dell'app Web **https://\<your-webapp-name>.azurewebsites.net**.

13. Se l'applicazione non è stata distribuita, si riceverà la pagina dell'app Web predefinita:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Pagina dell'app Web predefinita." border="true":::

18. Chiudere la connessione a **myVM**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e l'app Web seguendo questa procedura:

1. Nel menu a sinistra selezionare **Gruppi di risorse**.

2. Selezionare **CreatePrivateEndpointQS-rg**.

3. Selezionare **Elimina gruppo di risorse**.

4. Immettere **CreatePrivateEndpointQS-rg** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE**.

5. Selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un endpoint privato per un'app Web di Azure.

La macchina virtuale è stata usata per testare la connettività in modo sicuro all'app Web tramite l'endpoint privato.



Per altre informazioni sui servizi che supportano un endpoint privato, vedere:
> [!div class="nextstepaction"]
> [Disponibilità del collegamento privato](private-link-overview.md#availability)
