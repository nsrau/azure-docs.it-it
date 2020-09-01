---
title: Usare il firewall di Azure per controllare il traffico destinato a un endpoint privato
titleSuffix: Azure Private Link
description: Informazioni su come ispezionare il traffico destinato a un endpoint privato tramite il firewall di Azure.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 734d52dadbb849925303febb0d3d1195bbddb0df
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236687"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Usare il firewall di Azure per controllare il traffico destinato a un endpoint privato

Endpoint privato di Azure è il blocco predefinito fondamentale per il collegamento privato di Azure. Gli endpoint privati consentono alle risorse di Azure distribuite in una rete virtuale di comunicare privatamente con risorse di collegamento privato.

Gli endpoint privati consentono alle risorse di accedere al servizio di collegamento privato distribuito in una rete virtuale. L'accesso all'endpoint privato tramite il peering di rete virtuale e le connessioni di rete locali estendono la connettività.

Potrebbe essere necessario controllare o bloccare il traffico dai client ai servizi esposti tramite endpoint privati. Completare questa ispezione usando il [firewall di Azure](../firewall/overview.md) o un'appliance virtuale di rete di terze parti.

Si applicano le limitazioni seguenti:

* I gruppi di sicurezza di rete (gruppi) non si applicano agli endpoint privati
* Le route definite dall'utente (UDR) non si applicano agli endpoint privati
* È possibile collegare una singola tabella di route a una subnet
* Una tabella di route supporta fino a 400 Route

Il firewall di Azure filtra il traffico usando uno dei seguenti:

* [FQDN nelle regole di rete](../firewall/fqdn-filtering-network-rules.md) per i protocolli TCP e UDP
* [FQDN nelle regole dell'applicazione](../firewall/features.md#application-fqdn-filtering-rules) per http, HTTPS e MSSQL. 

La maggior parte dei servizi esposti tramite endpoint privati usa HTTPS. Quando si usa SQL di Azure, è consigliabile usare le regole dell'applicazione rispetto alle regole di rete.

> [!NOTE]
> Il filtro FQDN di SQL è supportato solo in modalità [proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). La modalità **proxy** può comportare una latenza maggiore rispetto al *Reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando FQDN nelle regole di rete del firewall.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Scenario 1: architettura Hub e spoke-rete virtuale dedicata per endpoint privati

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Rete virtuale dedicata per endpoint privati" border="true":::

Questo scenario è l'architettura più espandibile per connettersi privatamente a più servizi di Azure usando endpoint privati. Viene creata una route che punta allo spazio degli indirizzi di rete in cui vengono distribuiti gli endpoint privati. Questa configurazione riduce il sovraccarico amministrativo e impedisce l'esecuzione fino al limite di 400 Route.

Le connessioni da una rete virtuale client al firewall di Azure in una rete virtuale dell'hub verranno addebitate se viene effettuato il peering delle reti virtuali.

Per ulteriori informazioni sugli addebiti relativi alle connessioni con reti virtuali con peering, vedere la sezione Domande frequenti della pagina dei [prezzi](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Questo scenario può essere implementato usando le regole di rete di terze parti o del firewall di Azure anziché le regole dell'applicazione.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Scenario 2: architettura Hub e spoke-rete virtuale condivisa per endpoint privati e macchine virtuali

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Endpoint privati e macchine virtuali nella stessa rete virtuale" border="true":::

Questo scenario viene implementato nei casi seguenti:

* Non è possibile avere una rete virtuale dedicata per gli endpoint privati

* Quando solo alcuni servizi vengono esposti nella rete virtuale usando endpoint privati

Le macchine virtuali avranno/32 le route di sistema che puntano a ogni endpoint privato. Una route per ogni endpoint privato è configurata per instradare il traffico attraverso il firewall di Azure. 

Il sovraccarico amministrativo associato alla manutenzione della tabella di route aumenta quando i servizi vengono esposti nella rete virtuale. Aumenta anche la possibilità di raggiungere il limite di route.

A seconda dell'architettura complessiva, è possibile eseguire il limite di 400 Route. Quando possibile, è consigliabile usare lo scenario 1.

Le connessioni da una rete virtuale client al firewall di Azure in una rete virtuale dell'hub verranno addebitate se viene effettuato il peering delle reti virtuali.

Per ulteriori informazioni sugli addebiti relativi alle connessioni con reti virtuali con peering, vedere la sezione Domande frequenti della pagina dei [prezzi](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Questo scenario può essere implementato usando le regole di rete di terze parti o del firewall di Azure anziché le regole dell'applicazione.

## <a name="scenario-3-single-virtual-network"></a>Scenario 3: rete virtuale singola

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Rete virtuale singola" border="true":::

Esistono alcune limitazioni all'implementazione: una migrazione a un'architettura Hub e spoke non è possibile. Si applicano le stesse considerazioni dello scenario 2. In questo scenario, gli addebiti per il peering di rete virtuale non sono applicabili.

>[!NOTE]
> Se si vuole implementare questo scenario usando un appliance virtuale di rete di terze parti o un firewall di Azure, le regole di rete anziché le regole dell'applicazione sono necessarie per SNAT il traffico destinato agli endpoint privati. In caso contrario, la comunicazione tra le macchine virtuali e gli endpoint privati avrà esito negativo.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Scenario 4: traffico locale verso endpoint privati

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Traffico locale verso endpoint privati" border="true":::

Questa architettura può essere implementata se è stata configurata la connettività con la rete locale usando: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [VPN da sito a sito](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md) 

Se i requisiti di sicurezza richiedono il routing del traffico client ai servizi esposti tramite endpoint privati tramite un'appliance di sicurezza, distribuire questo scenario.

Si applicano le stesse considerazioni illustrate nello scenario 2. In questo scenario non sono previsti addebiti per il peering di rete virtuale. Per altre informazioni su come configurare i server DNS per consentire ai carichi di lavoro locali di accedere a endpoint privati, vedere [carichi di lavoro locali tramite un server di un server di un server di server](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> Se si vuole implementare questo scenario usando un appliance virtuale di rete di terze parti o un firewall di Azure, le regole di rete anziché le regole dell'applicazione sono necessarie per SNAT il traffico destinato agli endpoint privati. In caso contrario, la comunicazione tra le macchine virtuali e gli endpoint privati avrà esito negativo.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.
* Un'area di lavoro Log Analytics.  

Vedere [creare un'area di lavoro log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) per creare un'area di lavoro se non ne è già presente una nella sottoscrizione.


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Questa sezione illustra come creare una rete virtuale e una subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato. Un database SQL di Azure viene usato come servizio di esempio.

## <a name="virtual-networks-and-parameters"></a>Reti virtuali e parametri

Creare tre reti virtuali e le subnet corrispondenti per:

* Contenere il firewall di Azure usato per limitare la comunicazione tra la macchina virtuale e l'endpoint privato.
* Ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato.
* Ospitare l'endpoint privato.

Sostituire i parametri seguenti nei passaggi con le informazioni riportate di seguito:

### <a name="azure-firewall-network"></a>Rete firewall di Azure
| Parametro                   | valore                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | Stati Uniti centro-meridionali      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Rete della macchina virtuale
| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | Stati Uniti centro-meridionali      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Rete di endpoint privati
| Parametro                   | valore                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | Stati Uniti centro-meridionali      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Ripetere i passaggi da 1 a 9 per creare le reti virtuali per l'hosting delle risorse della macchina virtuale e dell'endpoint privato.

### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **calcolo**  >  **macchina virtuale**.

2. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo di risorse è stato creato nella sezione precedente.  |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM**. |
    | Area | Selezionare **(Stati Uniti) Stati Uniti centro-meridionali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Ubuntu Server 18,04 LTS-Gen1**. |
    | Dimensioni | Selezionare **Standard_B2s**. |
    | **Account amministratore** |  |
    | Tipo di autenticazione | selezionare **Password**. |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **Regole porta in ingresso** |  |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |||

3. Selezionare **Avanti: Dischi**.

4. In **Crea macchina virtuale - Dischi** mantenere le impostazioni predefinite e selezionare **Avanti: Rete**.

5. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Selezionare **myVMVNet**.  |
    | Subnet | Selezionare **elemento vmsubnet (10.1.0.0/24)**.|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **SSH**.|
    ||

6. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

7. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="deploy-the-firewall"></a>Distribuire il firewall

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

2. Digitare **firewall** nella casella di ricerca e premere **INVIO**.

3. Selezionare **Firewall**, quindi **Crea**.

4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**.  |
    | **Dettagli istanza** |  |
    | Nome | Immettere **myAzureFirewall**. |
    | Area | Selezionare **Stati Uniti centro-meridionali**. |
    | Zona di disponibilità | Lasciare il valore predefinito **Nessuna**. |
    | Scegliere una rete virtuale    |    Selezionare **Usa esistente**.    |
    | Rete virtuale    |    Selezionare **myAzFwVNet**.    |
    | Indirizzo IP pubblico    |    Selezionare **Aggiungi nuovo** e in nome immettere **webfirewall-IP**.    |
    | Tunneling forzato    | Lasciare **disabilitato**il valore predefinito.    |
    |||
5. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

6. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="enable-firewall-logs"></a>Abilitare i log del firewall

In questa sezione vengono abilitati i log del firewall.

1. Nella portale di Azure selezionare tutte le **risorse** nel menu a sinistra.

2. Selezionare il firewall **myAzureFirewall** nell'elenco di risorse.

3. In **monitoraggio** nelle impostazioni del firewall selezionare **impostazioni di diagnostica** .

4. Selezionare **+ Aggiungi** impostazioni di diagnostica nelle impostazioni di diagnostica.

5. In **impostazioni di diagnostica**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome dell'impostazione di diagnostica | Immettere **myDiagSetting**. |
    | Dettagli categoria | |
    | log | Selezionare **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**. |
    | Dettagli destinazione | Selezionare **Invia a Log Analytics**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Area di lavoro Log Analytics | Selezionare l'area di lavoro Log Analytics. |

6. Selezionare **Salva**.

## <a name="create-azure-sql-database"></a>Creare un database SQL di Azure

In questa sezione viene creato un database SQL privato.

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **database**  >  **SQL database**.

2. In **create SQL database-nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo di risorse è stato creato nella sezione precedente.|
    | **Dettagli database** |  |
    | Nome database  | Immettere **mydatabase**.  |
    | Server | Selezionare **Crea nuovo** e immettere le informazioni riportate di seguito.    |
    | Nome server | Immettere **MYDBSERVER**. Se viene accettato questo nome, immettere un nome univoco.   |
    | Accesso amministratore server | Immettere un nome a scelta. |
    | Password    |    Immettere una password a scelta.    |
    | Confirm Password | Reimmettere la password    |
    | Location    | Selezionare **(Stati Uniti) Stati Uniti centro-meridionali**.    |
    | Si vuole usare il pool elastico SQL    | Lasciare il valore predefinito **No**. |
    | Calcolo e archiviazione | Lasciare il valore predefinito **per utilizzo generico quinta generazione, 2 Vcore, 32 GB di spazio di archiviazione**. |
    |||

3. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

4. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="create-private-endpoint"></a>Crea endpoint privato

In questa sezione viene creato un endpoint privato per il database SQL di Azure nella sezione precedente.

1. Nella portale di Azure selezionare tutte le **risorse** nel menu a sinistra.

2. Selezionare il **MYDBSERVER** di Azure SQL Server nell'elenco dei servizi.  Se è stato utilizzato un nome di server diverso, scegliere tale nome.

3. In Impostazioni server selezionare **connessioni endpoint privato** in **sicurezza**.

4. Selezionare **+ Endpoint privato**.

5. In **Crea un endpoint privato**immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** | |
    | Nome | Immettere **SQLPrivateEndpoint**. |
    | Area | Selezionare **(Stati Uniti) Stati Uniti centro-meridionali.** |

6. Selezionare la scheda **risorsa** o selezionare **Avanti: risorsa** nella parte inferiore della pagina.

7. Nella scheda **risorsa** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Metodo di connessione | Selezionare **Connettersi a una risorsa di Azure nella directory**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.Sql/servers**. |
    | Risorsa | Selezionare **MYDBSERVER** o il nome del server creato nel passaggio precedente.
    | Sottorisorsa di destinazione | Selezionare **sqlServer**. |

8. Selezionare la scheda **configurazione** o selezionare **Avanti: configurazione** nella parte inferiore della pagina.

9. Nella scheda **configurazione** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Rete** | |
    | Rete virtuale | Selezionare **myPEVnet**. |
    | Subnet | Selezionare **PrivateEndpointSubnet**. |
    | **Integrazione di DNS privato** | |
    | Integra con la zona DNS privato | Selezionare **Sì**. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Zone DNS privato | Lasciare il valore predefinito **privatelink.database.Windows.NET**. |

10. Selezionare la scheda **Verifica + crea** oppure selezionare **Verifica + crea** nella parte inferiore della pagina.

11. Selezionare **Create** (Crea).

12. Dopo aver creato l'endpoint, selezionare **firewall e reti virtuali** in **sicurezza**.

13. In **firewall e reti virtuali**selezionare **Sì** accanto a Consenti ai **Servizi e alle risorse di Azure di accedere al server**.

14. Selezionare **Salva**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Connettere le reti virtuali tramite il peering di rete virtuale

In questa sezione verranno connesse le reti virtuali **myVMVNet** e **myPEVNet** a **myAzFwVNet** mediante il peering. Non ci sarà connettività diretta tra **myVMVNet** e **myPEVNet**.

1. Nella barra di ricerca del portale immettere **myAzFwVNet**.

2. Selezionare **peer** nel menu **Impostazioni** e selezionare **+ Aggiungi**.

3. In **Aggiungi peering** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome del peering da myAzFwVNet a rete virtuale remota | Immettere **myAzFwVNet-myVMVNet**. |
    | **Dettagli peer** |  |
    | Modello di distribuzione della rete virtuale  | Lasciare la **Gestione risorse**predefinita.  |
    | Conosco l'ID della risorsa | lasciare la casella deselezionata.    |
    | Subscription | Selezionare la propria sottoscrizione.    |
    | Rete virtuale | Selezionare **myVMVNet**. |
    | Nome del peering dalla rete virtuale remota a myAzFwVNet    |    Immettere **myVMVNet-myAzFwVNet**.    |
    | **Configurazione** | |
    | **Configurare le impostazioni di accesso alla rete virtuale** | |
    | Consenti accesso alla rete virtuale da myAzFwVNet a rete virtuale remota | Lasciare il valore predefinito, **Abilitata**.    |
    | Consenti accesso alla rete virtuale da una rete virtuale remota a myAzFwVNet    | Lasciare il valore predefinito, **Abilitata**.    |
    | **Configurare le impostazioni del traffico con inoltri** | |
    | Consenti il traffico inviato dalla rete virtuale remota a myAzFwVNet    | Selezionare **Enabled**. |
    | Consenti il traffico trasmesso da myAzFwVNet alla rete virtuale remota | Selezionare **Enabled**. |
    | **Configurare le impostazioni di transito del gateway** | |
    | Consenti transito gateway | Lascia deselezionata |
    |||

4. Selezionare **OK**.

5. Selezionare **+ Aggiungi**.

6. In **Aggiungi peering** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome del peering da myAzFwVNet a rete virtuale remota | Immettere **myAzFwVNet-myPEVNet**. |
    | **Dettagli peer** |  |
    | Modello di distribuzione della rete virtuale  | Lasciare la **Gestione risorse**predefinita.  |
    | Conosco l'ID della risorsa | lasciare la casella deselezionata.    |
    | Subscription | Selezionare la propria sottoscrizione.    |
    | Rete virtuale | Selezionare **myPEVNet**. |
    | Nome del peering dalla rete virtuale remota a myAzFwVNet    |    Immettere **myPEVNet-myAzFwVNet**.    |
    | **Configurazione** | |
    | **Configurare le impostazioni di accesso alla rete virtuale** | |
    | Consenti accesso alla rete virtuale da myAzFwVNet a rete virtuale remota | Lasciare il valore predefinito, **Abilitata**.    |
    | Consenti accesso alla rete virtuale da una rete virtuale remota a myAzFwVNet    | Lasciare il valore predefinito, **Abilitata**.    |
    | **Configurare le impostazioni del traffico con inoltri** | |
    | Consenti il traffico inviato dalla rete virtuale remota a myAzFwVNet    | Selezionare **Enabled**. |
    | Consenti il traffico trasmesso da myAzFwVNet alla rete virtuale remota | Selezionare **Enabled**. |
    | **Configurare le impostazioni di transito del gateway** | |
    | Consenti transito gateway | Lascia deselezionata |

7. Selezionare **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Collegare le reti virtuali alla zona DNS privata

In questa sezione si collegheranno le reti virtuali **myVMVNet** e **MYAZFWVNET** alla zona DNS privata **privatelink.database.Windows.NET** . Questa zona è stata creata quando è stato creato l'endpoint privato. 

Il collegamento è necessario per la macchina virtuale e il firewall per la risoluzione del nome di dominio completo del database nell'indirizzo endpoint privato. La rete virtuale **myPEVNet** è stata collegata automaticamente al momento della creazione dell'endpoint privato.

>[!NOTE]
>Se non si collegano le reti virtuali della VM e del firewall alla zona DNS privata, sia la macchina virtuale che il firewall saranno comunque in grado di risolvere il nome di dominio completo SQL Server. Verranno risolti nell'indirizzo IP pubblico.

1. Nella barra di ricerca del portale immettere **privatelink. database**.

2. Selezionare **privatelink.database.Windows.NET** nei risultati della ricerca.

3. Selezionare **collegamenti reti virtuali** in **Impostazioni**.

4. Selezionare **+ Aggiungi**

5. In **Aggiungi collegamento rete virtuale** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome collegamento | Immettere il **collegamento a myVMVNet**. |
    | **Dettagli rete virtuale** |  |
    | Conosco l'ID risorsa della rete virtuale  | lasciare la casella deselezionata.  |
    | Subscription | Selezionare la propria sottoscrizione.    |
    | Rete virtuale | Selezionare **myVMVNet**. |
    | **CONFIGURATION** | |
    | Abilita registrazione automatica | lasciare la casella deselezionata.    |


6. Selezionare **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Configurare una regola dell'applicazione con FQDN SQL nel firewall di Azure

In questa sezione configurare una regola dell'applicazione per consentire la comunicazione tra **myVM** e l'endpoint privato per SQL Server **MYDBSERVER.database.Windows.NET**. 

Questa regola consente la comunicazione attraverso il firewall creato nei passaggi precedenti.

1. Nella barra di ricerca del portale immettere **myAzureFirewall**.

2. Selezionare **myAzureFirewall** nei risultati della ricerca.

3. Selezionare **regole** in **Impostazioni** nella Panoramica di **myAzureFirewall** .

4. Selezionare la scheda **Raccolta regole dell'applicazione**.

5. Selezionare **+ Aggiungi raccolta regole applicazione**.

6. In **Aggiungi raccolta regole applicazione** immettere o selezionare le seguenti informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **SQLPrivateEndpoint**. |
    | Priorità | Immettere **100**. |
    | Azione | Immettere **Allow**. |
    | **Regole** |  |
    | **Tag FQDN** | |
    | Nome  | Lasciare vuoto.  |
    | Tipo di origine | Lasciare l' **indirizzo IP**predefinito.    |
    | Source (Sorgente) | Lasciare vuoto. |
    | Tag FQDN | Lasciare selezionata l'opzione predefinita **0**. |
    | **FQDN di destinazione** | |
    | Nome | Immettere **SQLPrivateEndpoint**.    |
    | Tipo di origine | Lasciare l' **indirizzo IP**predefinito. |
    | Source (Sorgente) | Immettere **10.1.0.0/16**. |
    | Protocollo: porta | Immettere **MSSQL: 1433**. |
    | FQDN di destinazione | Immettere **MYDBSERVER.database.Windows.NET**. |
    |||

7. Selezionare **Aggiungi**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Instradare il traffico tra la macchina virtuale e l'endpoint privato tramite il firewall di Azure

Non è stato creato un peering di rete virtuale direttamente tra le reti virtuali **myVMVNet** e **myPEVNet**. Il **myVM** della macchina virtuale non ha una route per l'endpoint privato creato. 

In questa sezione verrà creata una tabella di route con una route personalizzata. 

La route invia il traffico dalla subnet **myVM** allo spazio degli indirizzi della rete virtuale **MyPEVNet**tramite il firewall di Azure.

1. Nel menu del portale di Azure o nella **home page** selezionare **Crea una risorsa**.

2. Digitare **tabella di route** nella casella di ricerca e premere **invio**.

3. Selezionare **tabella di route** , quindi selezionare **Crea**.

4. Nella pagina **Crea tabella di route** usare la tabella seguente per configurare la tabella di route:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**.  |
    | **Dettagli istanza** |  |
    | Area | Selezionare **Stati Uniti centro-meridionali**. |
    | Nome | Immettere **elemento vmsubnet-AzureFirewall**. |
    | Propagazione delle route del gateway | Selezionare **No**. |

5. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

6. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

7. Al termine della distribuzione selezionare **Vai alla risorsa**.

8. Selezionare **Route** in **Impostazioni**.

9. Selezionare **+ Aggiungi**.

10. Nella pagina **Aggiungi route** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome route | Immettere **myVMsubnet-privateendpoint**. |
    | Prefisso indirizzo | Immettere **10.2.0.0/16**.  |
    | Tipo hop successivo | Selezionare **Appliance virtuale**. |
    | Indirizzo hop successivo | Immettere **10.0.0.4**. |

11. Selezionare **OK**.

12. Selezionare **subnet** in **Impostazioni**.

13. Selezionare **+ Associa**.

14. Nella pagina **associa subnet** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Selezionare **myVMVNet**. |
    | Subnet | Selezionare **elemento vmsubnet**.  |

15. Selezionare **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Connettersi alla macchina virtuale dal computer client

Connettersi alla macchina virtuale **myVm** da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere **myVm-IP**.

2. Selezionare **myVM-IP** nei risultati della ricerca.

3. Copiare o annotare il valore in **indirizzo IP**.

4. Se si usa Windows 10, eseguire il comando seguente usando PowerShell. Per altre versioni di client Windows, usare un client SSH come [Putty](https://www.putty.org/):

* Sostituire **username** con il nome utente dell'amministratore immesso durante la creazione della macchina virtuale.

* Sostituire **IPAddress** con l'indirizzo IP del passaggio precedente.

    ```bash
    ssh username@IPaddress
    ```

5. Immettere la password specificata durante la creazione di **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Accedere SQL Server privatamente dalla macchina virtuale

In questa sezione si connetterà privatamente al database SQL usando l'endpoint privato.

1. Immettere `nslookup mydbserver.database.windows.net`
    
    Verrà visualizzato un messaggio simile al seguente:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Installare [SQL Server gli strumenti da riga di comando](https://docs.microsoft.com/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools).

3. Eseguire il comando seguente per connettersi al SQL Server. Usare l'amministratore del server e la password definiti al momento della creazione del SQL Server nei passaggi precedenti.

* Sostituire **\<ServerAdmin>** con il nome utente amministratore immesso durante la creazione di SQL Server.

* Sostituire **\<YourPassword>** con la password amministratore immessa durante la creazione di SQL Server.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Verrà visualizzato un prompt dei comandi SQL per l'accesso riuscito. Immettere **Exit** per uscire dallo strumento **SQLCMD** .

5. Chiudere la connessione a **myVM** immettendo **Exit**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Convalidare il traffico nei log del firewall di Azure

1. Nella portale di Azure selezionare **tutte le risorse** e selezionare l'area di lavoro log Analytics.

2. Selezionare **log** in **generale** nella pagina area di lavoro log Analytics.

3. Selezionare **il pulsante blu per iniziare.**

4. Nella finestra **query di esempio** selezionare **Firewall** in **tutte le query**.

5. Selezionare il pulsante **Esegui** in **log regole applicazione dati**.

6. Nell'output della query di log verificare che **MYDBSERVER.database.Windows.NET** sia elencato in **FQDN** e che **SQLPrivateEndpoint** sia elencato in **RuleCollection**.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso delle risorse, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere **myResourceGroup** nella casella **Cerca** nella parte superiore del portale e selezionare **myResourceGroup** nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere **myResourceGroup** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati esaminati diversi scenari che è possibile usare per limitare il traffico tra una macchina virtuale e un endpoint privato usando il firewall di Azure. 

È stata effettuata la connessione alla macchina virtuale e la comunicazione protetta con il database tramite il firewall di Azure con un collegamento privato.

Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](private-endpoint-overview.md).
