---
title: "Esercitazione: Connettersi a un'app Web con un endpoint privato di Azure"
titleSuffix: Azure Private Link
description: Questa esercitazione illustra come usare un endpoint privato di Azure per connettersi privatamente a un'app Web.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896974"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Esercitazione: Connettersi a un'app Web con un endpoint privato di Azure

Un endpoint privato di Azure è il blocco costitutivo fondamentale del collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con le risorse collegamento privato.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e un host bastion.
> * Creare una macchina virtuale.
> * Creare un'app Web.
> * Creare un endpoint privato.
> * Testare la connettività con l'endpoint privato dell'app Web.

Se non si possiede una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!Note]
> L'endpoint privato è disponibile nelle aree pubbliche per le app Web Windows del livello PremiumV2, PremiumV3, le app Web Linux e il piano Premium di Funzioni di Azure (talvolta definito piano elastico Premium). 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale** :

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Europa occidentale** |

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
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base** :

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Area | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete** , oppure selezionare **Avanti: Dischi** , quindi **Avanti: Rete**.
  
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

## <a name="create-web-app"></a>Crea app Web

In questa sezione si creerà un'app Web.

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Archiviazione** > **App Web** oppure immettere **App Web** nella casella di ricerca.

2. Nella scheda **Informazioni di base** della pagina **Crea app Web** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome | Immettere **mywebapp**. Se il nome non è disponibile, immetterne un altro univoco. |
    | Pubblica | Selezionare **Codice**. |
    | Stack di runtime | Selezionare **.NET Core 3.1 (LTS)** . |
    | Sistema operativo | Selezionare **Windows**. |
    | Area | Selezionare **Europa occidentale** |
    | **Piano di servizio app** |  |
    | Piano Windows (Europa occidentale) | Selezionare **Crea nuovo**. </br> Immettere **myServicePlan** in **Nome**. |
    | SKU e dimensioni | Selezionare **Modifica dimensioni**. </br> Selezionare **P2V2** nella schermata **Selezione specifiche**. </br> Selezionare **Applica**. |
   
3. Selezionare **Rivedi e crea**.

4. Selezionare **Create** (Crea).

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Scheda Informazioni di base dell'opzione Crea app Web nel portale di Azure." border="true":::

## <a name="create-private-endpoint"></a>Creare un endpoint privato

1. Nel menu a sinistra selezionare **Tutte le risorse** > **mywebapp** o il nome scelto durante la creazione.

2. Nella panoramica dell'app Web selezionare **Impostazioni** > **Rete**.

3. In **Rete** selezionare **Configurare le connessioni endpoint privato**.

4. Selezionare **+ Aggiungi** nella schermata **Connessioni endpoint privato**.

5. Nella schermata **Aggiungi endpoint privato** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **mywebappendpoint**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet**. |
    | Integra con la zona DNS privato | Selezionare **Sì**. |

6. Selezionare **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi all'app Web tramite l'endpoint privato.

1. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

2. Selezionare **myResourceGroup**.

3. Selezionare **myVM**.

4. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

7. Aprire Windows PowerShell nel server dopo la connessione.

8. Immettere `nslookup <webapp-name>.azurewebsites.net`. Sostituire **\<webapp-name>** con il nome dell'app Web creata nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Per il nome dell'app Web viene restituito l'indirizzo IP privato **10.1.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.

9. Aprire un Web browser nel computer locale e immettere l'URL esterno dell'app Web **https://\<webapp-name>.azurewebsites.net**.

10. Verificare di ricevere una pagina **403**. Questa pagina indica che l'app Web non è accessibile esternamente.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Pagina 403 per l'indirizzo esterno dell'app Web." border="true":::

11. Nella connessione bastion a **myVM** aprire Internet Explorer.

12. Immettere l'URL dell'app Web **https://\<webapp-name>.azurewebsites.net**.

13. Verificare di ricevere la pagina dell'app Web predefinita.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Pagina dell'app Web predefinita." border="true":::

18. Chiudere la connessione a **myVM**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e l'app Web seguendo questa procedura:

1. Nel menu a sinistra selezionare **Gruppi di risorse**.

2. Selezionare **myResourceGroup**.

3. Selezionare **Elimina gruppo di risorse**.

4. In **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup**.

5. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un servizio Collegamento privato:
> [!div class="nextstepaction"]
> [Creare un servizio Collegamento privato](create-private-link-service-portal.md)
