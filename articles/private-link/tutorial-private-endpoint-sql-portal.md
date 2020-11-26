---
title: 'Esercitazione: Connettersi a un server SQL di Azure con un endpoint privato di Azure - Portale'
description: Questa esercitazione illustra come creare un server SQL di Azure con un endpoint privato usando il portale di Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: a7eca29dc9390306ac6ad4e66eec75a25c2d33ff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522159"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Esercitazione: Connettersi a un server SQL di Azure con un endpoint privato di Azure - Portale di Azure

Un endpoint privato di Azure è il blocco costitutivo fondamentale del collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con le risorse collegamento privato.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e un host bastion.
> * Creare una macchina virtuale.
> * Creare un server di Azure SQL e un endpoint privato.
> * Testare la connettività con l'endpoint privato del server SQL.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
    | Gruppo di risorse   | Selezionare **CreateSQLEndpointTutorial-rg** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Stati Uniti orientali**. |

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
    | Gruppo di risorse | Selezionare **CreateSQLEndpointTutorial** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Area | Selezionare **Stati Uniti orientali**. |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Creare un server di Azure SQL e un endpoint privato

In questa sezione si creerà un server SQL in Azure. 

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Database** > **Database SQL**.

1. Nella scheda **Informazioni di base** di **Crea database SQL** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **CreateSQLEndpointTutorial**. Questo gruppo di risorse è stato creato nella sezione precedente.|
    | **Dettagli database** |  |
    | Nome del database  | Immettere **mysqldatabase**. Se il nome è già usato, creare un nome univoco. |
    | Server | Selezionare **Crea nuovo**. |

6. In **Nuovo server** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome server  | immettere **mysqlserver**. Se il nome è già usato, creare un nome univoco.|
    | Accesso amministratore server | Immettere un nome amministratore a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 8 caratteri e soddisfare i requisiti definiti. |
    | Location | Selezionare **Stati Uniti orientali**. |
    
7. Selezionare **OK**.

8. Selezionare la scheda **Rete** oppure il pulsante **Avanti: Rete**.

9. Nella scheda **Rete** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Connettività di rete** | |
    | Metodo di connettività | Selezionare **Endpoint privato**. |
   
10. Selezionare **+ Aggiungi endpoint privato** in **Endpoint privati**.

11. In **Crea endpoint privato** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **CreateSQLEndpointTutorial**. |
    | Location | Selezionare **Stati Uniti orientali**. |
    | Name | Immettere **myPrivateSQLendpoint**. |
    | Sottorisorsa di destinazione | Selezionare **SQLServer**. |
    | **Rete** |  |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet**. |
    | **Integrazione DNS privato** | |
    | Integra con la zona DNS privato | Lasciare l'impostazione predefinita **Sì**. |
    | Zona DNS privato | Lasciare l'impostazione predefinita **(Nuovo) privatelink.database.windows.net**. |

12. Selezionare **OK**. 

13. Selezionare **Rivedi e crea**.

14. Selezionare **Create** (Crea).

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi al server SQL tramite l'endpoint privato.

1. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

2. Selezionare **CreateSQLEndpointTutorial**.

3. Selezionare **myVM**.

4. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

7. Aprire Windows PowerShell nel server dopo la connessione.

8. Immettere `nslookup <sqlserver-name>.database.windows.net`. Sostituire **\<sqlserver-name>** con il nome del server SQL creato nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Per il nome del server SQL viene restituito l'indirizzo IP privato **10.1.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.


9. Installare [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) in **myVM**.

10. Aprire **SQL Server Management Studio**.

4. In **Connetti a server** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server | Selezionare **Motore di database**.|
    | Nome server | Immettere **\<sqlserver-name>.database.windows.net** |
    | Autenticazione | Selezionare **Autenticazione di SQL Server**. |
    | Nome utente | Immettere il nome utente specificato durante la creazione del server |
    | Password | Immettere la password specificata durante la creazione del server |
    | Memorizza password | Selezionare **Sì**. |

1. Selezionare **Connetti**.
2. Esplorare i database dal menu a sinistra.
3. (Facoltativo) Creare o eseguire query sulle informazioni di **mysqldatabase**.
4. Chiudere la connessione Desktop remoto a **myVm**. 

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno dell'endpoint privato, del server SQL e della macchina virtuale, rimuovere il gruppo di risorse e tutte le risorse che contiene: 
1. Immettere **CreateSQLEndpointTutorial** nella casella **Cerca** nella parte superiore del portale e selezionare **CreateSQLEndpointTutorial** nei risultati della ricerca. 
2. Selezionare **Elimina gruppo di risorse**. 
3. Immettere CreateSQLEndpointTutorial per **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i componenti seguenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un server SQL con un endpoint privato.

La macchina virtuale è stata usata per testare la connettività in modo sicuro al server SQL tramite l'endpoint privato.

Informazioni su come creare un servizio Collegamento privato:
> [!div class="nextstepaction"]
> [Creare un servizio Collegamento privato](create-private-link-service-portal.md)