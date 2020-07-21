---
title: Supporto del servizio Device provisioning (DPS) di Azure per le reti virtuali
description: Come usare il modello di connettività delle reti virtuali con il servizio Device provisioning (DPS) di Azure
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: 97168c95893ffdea99180c997265f259176ed07f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531538"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Supporto del servizio Device provisioning in hub Azure (DPS) per le reti virtuali

Questo articolo presenta il modello di connettività della rete virtuale (VNET) per i dispositivi Internet per il provisioning con hub cose usando DPS. Questo modello fornisce la connettività privata tra i dispositivi, DPS e l'hub Internet all'interno di una VNET di Azure di proprietà del cliente. 

Nella maggior parte degli scenari in cui DPS è configurato con un VNET, anche l'hub Internet viene configurato nella stessa VNET. Per informazioni più specifiche sul supporto e la configurazione di VNET per gli hub Internet delle cose, vedere il [supporto della rete virtuale dell'hub Internet](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Introduzione

Per impostazione predefinita, i nomi host DPS vengono mappati a un endpoint pubblico con un indirizzo IP instradabile pubblicamente tramite Internet. Questo endpoint pubblico è visibile a tutti i clienti. È possibile tentare di accedere all'endpoint pubblico da dispositivi Internet su reti Wide e reti locali.

Per diversi motivi, i clienti potrebbero voler limitare la connettività alle risorse di Azure, ad esempio DP. Di seguito sono elencati alcuni motivi per cui questa scelta è consigliabile:

* Impedisci l'esposizione alla connessione sulla rete Internet pubblica. L'esposizione può essere ridotta introducendo livelli aggiuntivi di sicurezza tramite l'isolamento a livello di rete per le risorse dell'hub e delle risorse DPS

* Abilitazione di un'esperienza di connettività privata dalle risorse di rete locali per garantire che i dati e il traffico vengano trasmessi direttamente alla rete di backbone di Azure.

* Prevenzione degli attacchi di esfiltrazione da reti locali sensibili. 

* Applicazione di modelli di connettività consolidati a livello di Azure tramite [endpoint privati](../private-link/private-endpoint-overview.md).

Gli approcci comuni per limitare la connettività includono [le regole del filtro IP DPS](./iot-dps-ip-filtering.md) e la rete virtuale (VNET) con [endpoint privati](../private-link/private-endpoint-overview.md). Questo articolo descrive l'approccio di VNET per DPS usando endpoint privati. 

I dispositivi che operano nelle reti locali possono usare la [rete privata virtuale (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o il peering privato [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per connettersi a una VNET in Azure e accedere alle risorse DPS tramite endpoint privati. 

Un endpoint privato è un indirizzo IP privato allocato all'interno di un VNET di proprietà del cliente con cui è possibile accedere a una risorsa di Azure. Se si dispone di un endpoint privato per la risorsa DPS, sarà possibile consentire ai dispositivi che operano all'interno della VNET di richiedere il provisioning dalla risorsa DPS senza consentire il traffico all'endpoint pubblico.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i seguenti prerequisiti:

* La risorsa DPS è già stata creata e collegata agli hub Internet. Per istruzioni sulla configurazione di una nuova risorsa DPS, vedere [configurare il servizio Device provisioning in hub Internet con il portale di Azure](./quick-setup-auto-provision.md)

* È stato effettuato il provisioning di una VNET di Azure con una subnet in cui verrà creato l'endpoint privato. Per altre informazioni, vedere [creare una rete virtuale usando l'interfaccia](../virtual-network/quick-create-cli.md)della riga di comando di Azure.

* Per i dispositivi che operano all'interno di reti locali, configurare la [rete privata virtuale (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o il peering privato di [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) in Azure vnet.

## <a name="private-endpoint-limitations"></a>Limitazioni dell'endpoint privato

Si notino le seguenti limitazioni correnti per DPS quando si usano endpoint privati:

* Gli endpoint privati non funzioneranno con DPS quando la risorsa DPS e l'hub collegato si trovano in cloud diversi. Ad esempio, [Azure per enti pubblici e Azure globale](../azure-government/documentation-government-welcome.md).

* Attualmente, i [criteri di allocazione personalizzati con funzioni di Azure](how-to-use-custom-allocation-policies.md) per DPS non funzioneranno come endpoint VNET e privati. 

* Il supporto VNET DPS corrente è per l'ingresso di dati solo in DPS. I dati in uscita, ovvero il traffico da DPS a hub Internet, usano un meccanismo interno da servizio a servizio anziché una VNET dedicata. Il supporto per il blocco in uscita completo basato su VNET tra DPS e l'hub Internet non è attualmente disponibile.

* Il criterio di allocazione con latenza più bassa viene usato per assegnare un dispositivo all'hub delle cose con la latenza più bassa. Questo criterio di allocazione non è affidabile in un ambiente di rete virtuale. 

## <a name="set-up-a-private-endpoint"></a>Configurare un endpoint privato

Per configurare un endpoint privato, attenersi alla procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com/)aprire la risorsa DPS e fare clic sulla scheda **rete** . fare clic su **connessioni a endpoint privato** e su **+ endpoint privato**.

    ![Aggiungere un nuovo endpoint privato per DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Nella pagina _Crea un endpoint privato_ immettere le informazioni indicate nella tabella seguente.

    ![Configurare la risorsa a cui viene eseguito il mapping di un nuovo endpoint privato](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Campo | valore |
    | :---- | :-----|
    | **Sottoscrizione** | Scegliere la sottoscrizione di Azure desiderata per contenere l'endpoint privato.  |
    | **Gruppo di risorse** | Scegliere o creare un gruppo di risorse che contenga l'endpoint privato |
    | **Nome**       | Immettere un nome per l'endpoint privato |
    | **Area**     | L'area scelta deve essere identica a quella dell'area che contiene il VNET, ma non deve corrispondere alla risorsa DPS. |

    Fare clic su **Avanti: risorsa** per configurare la risorsa a cui punterà l'endpoint privato.

3. Nella pagina _Crea una risorsa endpoint privata_ immettere le informazioni indicate nella tabella seguente.

    ![Configurare la risorsa a cui viene eseguito il mapping di un nuovo endpoint privato](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Campo | valore |
    | :---- | :-----|
    | **Sottoscrizione**        | Scegliere la sottoscrizione di Azure che contiene la risorsa DPS a cui punterà l'endpoint privato.  |
    | **Tipo di risorsa**       | Scegliere **Microsoft. Devices/ProvisioningServices**. |
    | **Risorsa**            | Selezionare la risorsa DPS a cui viene mappato l'endpoint privato. |
    | **Sottorisorsa di destinazione** | Selezionare **iotDps**. |

    > [!TIP]
    > Le informazioni sull'impostazione **connessione a una risorsa di Azure in base all'ID risorsa o all'alias** sono disponibili nella sezione [richiedere un endpoint privato](#request-a-private-endpoint) in questo articolo.


    Fare clic su **Next: Configuration** per configurare il VNET per l'endpoint privato.

4. Nella pagina _Crea configurazione di un endpoint privato_ scegliere la rete virtuale e la subnet in cui creare l'endpoint privato.
 
    Fare clic su **Avanti: Tag**e, facoltativamente, specificare eventuali tag per la risorsa.

    ![Configurare la risorsa a cui viene eseguito il mapping di un nuovo endpoint privato](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Fare clic su **Verifica + crea** e quindi su **Crea** per creare la risorsa dell'endpoint privato.


## <a name="request-a-private-endpoint"></a>Richiedere un endpoint privato

È possibile richiedere un endpoint privato a una risorsa DPS in base all'ID risorsa. Per effettuare questa richiesta, è necessario che il proprietario della risorsa fornisca l'ID della risorsa. 

1. L'ID risorsa viene specificato nella scheda proprietà per la risorsa DPS, come illustrato di seguito.

    ![Scheda Proprietà DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Tenere presente che l'ID della risorsa contiene l'ID sottoscrizione. 

2. Una volta ottenuto l'ID risorsa, attenersi alla procedura descritta in [configurare un endpoint privato](#set-up-a-private-endpoint) nel passaggio 3 della pagina _creare una risorsa dell'endpoint privato_ . Fare clic su **Connetti a una risorsa di Azure in base all'ID risorsa o alias** e immettere le informazioni nella tabella seguente. 

    | Campo | valore |
    | :---- | :-----|
    | **ID risorsa o alias** | Immettere l'ID risorsa per la risorsa DPS. |
    | **Sottorisorsa di destinazione** | Immettere **iotDps** |
    | **Messaggio di richiesta** | Immettere un messaggio di richiesta per il proprietario della risorsa DPS.<br>ad esempio: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Fare clic su **Next: Configuration** per configurare il VNET per l'endpoint privato.

3. Nella pagina _Crea configurazione di un endpoint privato_ scegliere la rete virtuale e la subnet in cui creare l'endpoint privato.
 
    Fare clic su **Avanti: Tag**e, facoltativamente, specificare eventuali tag per la risorsa.

4. Fare clic su **Verifica + crea** e quindi su **Crea** per creare la richiesta di endpoint privato.

5. Il proprietario DPS visualizzerà la richiesta di endpoint privato nell'elenco **connessioni endpoint privato** nella scheda rete DPS. In questa pagina, il proprietario può **approvare** o **rifiutare** la richiesta di endpoint privato, come illustrato di seguito.

    ![Scheda Proprietà DPS](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Endpoint privati con prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per ulteriori informazioni sulle funzionalità di sicurezza di DPS:

* [Sicurezza](concepts-security.md)
* [Supporto di TLS 1,2](tls-support.md)
