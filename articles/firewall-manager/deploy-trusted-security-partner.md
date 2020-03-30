---
title: Distribuire un partner di sicurezza attendibile di Azure Firewall ManagerDeploy an Azure Firewall Manager trusted security partner
description: Informazioni su come distribuire una sicurezza attendibile di Azure Firewall Manager tramite il portale di Azure.Learn how to deploy an Azure Firewall Manager trusted security using the Azure portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931313"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Distribuire un partner di sicurezza affidabile (anteprima)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*I partner* di sicurezza affidabili in Azure Firewall Manager consentono di usare le offerte SECaaS (Network) di terze parti di terze parti per proteggere l'accesso a Internet per gli utenti.

Per altre informazioni sugli scenari supportati e sulle linee guida per le procedure consigliate, vedere [Che cosa sono i partner di sicurezza attendibili (anteprima)?](trusted-security-partners.md).

Per questa anteprima, i partner di sicurezza supportati sono i due sono i due sono **i** **due** partner di sicurezza supportati. Le aree supportate sono WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> L'anteprima di Gestione firewall di Azure deve essere abilitata in modo esplicito con il comando di PowerShell `Register-AzProviderFeature`.

Dal prompt dei comandi di PowerShell eseguire questi comandi:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. Eseguire questo comando per controllare lo stato della registrazione:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuire un provider di sicurezza di terze parti in un nuovo hubDeploy a third-party security provider in a new hub

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. In **Cerca**digitare **Firewall Manager** e selezionarlo in **Servizi**.
3. Passare a **Guida introduttiva**. Selezionare **Crea un hub virtuale protetto**. 
4. Immettere la sottoscrizione e il gruppo di risorse, selezionare un'area supportata e aggiungere le informazioni sull'hub e sulla rete WAN virtuale. 
5. **Distribuire il gateway VPN** è abilitato per impostazione predefinita. Per distribuire un partner di sicurezza attendibile nell'hub, è necessario un gateway VPN. 
6. Selezionare Avanti: Firewall di **AzureSelect Next: Azure Firewall**
   > [!NOTE]
   > I partner di sicurezza attendibili si connettono all'hub tramite tunnel del gateway VPN. Se si elimina il gateway VPN, le connessioni ai partner di sicurezza attendibili andranno perse.
7. Se si vuole distribuire Firewall di Azure per filtrare il traffico privato insieme al provider di servizi di terze parti per filtrare il traffico Internet, selezionare un criterio per Firewall di Azure.If you want to deploy Azure Firewall to filter private traffic along with third-party service provider to filter Internet traffic, select a policy for Azure Firewall. Vedere gli [scenari supportati](trusted-security-partners.md#key-scenarios).
8. Se si desidera distribuire solo un provider di sicurezza di terze parti nell'hub, selezionare Firewall di **Azure: Abilitato/Disabilitato** per impostarlo su **Disabilitato**. 
9. Selezionare **Avanti: Partner di sicurezza attendibili**.
10. Selezionare **Partner di sicurezza attendibile** per impostarlo su **Abilitato**. Selezionare un partner. 
11. Fare clic su **Avanti**. 
12. Esaminare il contenuto e quindi selezionare **Crea**.

La distribuzione del gateway VPN può richiedere più di 30 minuti.

Per verificare che l'hub sia stato creato, passare a Azure Firewall Manager->Hub protetti. Selezionare la pagina Panoramica >hub per visualizzare il nome del partner e lo stato Connessione di **sicurezza in sospeso**.

Dopo aver creato l'hub e configurato il partner di sicurezza, continuare a connettere il provider di sicurezza all'hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuire un provider di sicurezza di terze parti in un hub esistenteDeploy a third-party security provider in an existing hub

È inoltre possibile selezionare un hub esistente in una rete WAN virtuale e convertirlo in un *hub virtuale protetto.*

1. In **Guida introduttiva**selezionare **Converti hub esistenti**.
2. Selezionare una sottoscrizione e un hub esistente. Seguire la parte restante dei passaggi per distribuire un provider di terze parti in un nuovo hub.

Tenere presente che è necessario distribuire un gateway VPN per convertire un hub esistente in un hub protetto con provider di terze parti.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurare provider di sicurezza di terze parti per la connessione a un hub protettoConfigure third-party security providers to connect to a secured hub

Per configurare i tunnel nel gateway VPN dell'hub virtuale, i provider di terze parti devono disporre dei diritti di accesso all'hub. A tale scopo, associare un'entità servizio alla sottoscrizione o al gruppo di risorse e concedere i diritti di accesso. È quindi necessario assegnare queste credenziali alla terza parte utilizzando il proprio portale.

1. Creare un'entità servizio di Azure Active Directory (AD): è possibile ignorare l'URL di reindirizzamento. 

   [Procedure: Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Aggiungere i diritti di accesso e l'ambito per l'entità servizio.
   [Procedure: Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > È possibile limitare l'accesso solo al gruppo di risorse per un controllo più granulare.
3. Seguire le istruzioni di integrazione della rete WAN virtuale di Microsoft Azure per:Follow the ['Scaler: Configuring a Microsoft Azure Virtual WAN Integration](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) instructions to:

   - Accedere al portale per i partner e aggiungere le credenziali per concedere al partner attendibile l'accesso all'hub protetto.
   - Sincronizzare gli hub virtuali nel portale partner e configurare il tunnel con l'hub virtuale. È possibile eseguire questa operazione dopo la convalida delle credenziali di autenticazione di Azure AD.
   
4. È possibile esaminare lo stato di creazione del tunnel nel portale WAN virtuale di Azure in Azure.You can look at the tunnel creation status on the Azure Virtual WAN portal in Azure. Dopo aver visualizzato i tunnel **connessi** sia in Azure che nel portale partner, continuare con i passaggi successivi per configurare le route per selezionare le filiali e le reti virtuali che devono inviare traffico Internet al partner.

## <a name="configure-route-settings"></a>Configurare le impostazioni del percorso

1. Passare a Gestione firewall di Azure -> Hub protetti. 
2. Selezionare un hub. Lo stato dell'hub dovrebbe ora essere **Il provisioning** anziché la connessione di **sicurezza in sospeso**.

   Verificare che il provider di terze parti possa connettersi all'hub. I tunnel nel gateway VPN devono essere in stato **Connesso.** Questo stato è più riflettente dell'integrità della connessione tra l'hub e il partner di terze parti, rispetto allo stato precedente.
3. Selezionare l'hub e passare a **Impostazioni percorso**.

   Quando si distribuisce un provider di terze parti nell'hub, l'hub viene convertito in un *hub virtuale protetto.* Ciò garantisce che il provider di terze parti annunci una route 0.0.0.0/0 (predefinita) all'hub. Tuttavia, le connessioni di rete virtuale e i siti connessi all'hub non ottengono questa route a meno che non si acconsenti esplicitamente a quali connessioni devono ottenere questa route predefinita.
4. In **Traffico Internet**selezionare Da rete a **Internet** o Da ramo **a Internet** oppure entrambe le route configurate per l'invio tramite terze parti.

   Ciò indica solo il tipo di traffico che deve essere instradato all'hub, ma non influisce ancora sulle route su reti virtuali o rami. Queste route non vengono propagate a tutte le reti virtuali/filiali collegate all'hub per impostazione predefinita.
5. È necessario selezionare **le connessioni protette** e selezionare le connessioni su cui devono essere impostate queste route. Ciò indica quali reti virtuali/filiali possono iniziare a inviare traffico Internet al provider di terze parti.
6. In **Impostazioni percorso**selezionare Connessioni **protette** in Traffico Internet, quindi selezionare la rete virtuale o le filiali *(siti* nella WAN virtuale) da proteggere. Selezionare **Traffico Internet sicuro**.
   ![Traffico Internet sicuro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Tornare alla pagina hub. Lo stato del partner di **sicurezza attendibile** dell'hub dovrebbe ora essere **Protetto**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Traffico Internet su succursale o rete virtuale tramite servizio di terze parti

Successivamente, è possibile verificare se le macchine virtuali della rete virtuale o il sito di succursale può accedere a Internet e verificare che il traffico scortra verso il servizio di terze parti.

Dopo aver completato i passaggi di impostazione del percorso, alle macchine virtuali della rete virtuale e ai siti di succursale viene inviato uno 0/0 al percorso di servizio di terze parti. Non è possibile RDP o SSH in queste macchine virtuali. Per accedere, è possibile distribuire il servizio [Bastion](../bastion/bastion-overview.md) di Azure in una rete virtuale con peering.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Proteggere la rete cloud con Azure Firewall Manager Preview usando il portale di AzureTutorial: Secure your cloud network with Azure Firewall Manager Preview using the Azure portal](secure-cloud-network.md)




