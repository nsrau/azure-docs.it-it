---
title: Distribuire un partner di sicurezza attendibile di Azure Firewall Manager
description: Informazioni su come distribuire una sicurezza attendibile di gestione firewall di Azure usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931313"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Distribuire un partner di sicurezza affidabile (anteprima)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Partner di sicurezza attendibili* in Azure Firewall Manager ti permette di usare le tue offerte di sicurezza come servizio (secar) di terze parti più comuni per proteggere l'accesso a Internet per gli utenti.

Per altre informazioni sugli scenari supportati e sulle procedure consigliate, vedere informazioni sui [partner di sicurezza attendibili (anteprima)](trusted-security-partners.md).

I partner di sicurezza supportati sono **ZScaler** e **iboss** per questa versione di anteprima. Le aree supportate sono WestCentralUS, NorthCentralUS, Westus, WestUS2 e Eastus.

## <a name="prerequisites"></a>prerequisiti

> [!IMPORTANT]
> L'anteprima di Gestione firewall di Azure deve essere abilitata in modo esplicito con il comando di PowerShell `Register-AzProviderFeature`.

Dal prompt dei comandi di PowerShell eseguire questi comandi:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. Eseguire questo comando per controllare lo stato della registrazione:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuire un provider di sicurezza di terze parti in un nuovo hub

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella casella di **ricerca**digitare **Firewall Manager** e selezionarlo in **Servizi**.
3. Passare a **Introduzione**. Selezionare **creare un hub virtuale protetto**. 
4. Immettere la sottoscrizione e il gruppo di risorse, selezionare un'area supportata e aggiungere le informazioni sull'hub e sulla rete WAN virtuale. 
5. La **distribuzione del gateway VPN** è abilitata per impostazione predefinita. Un gateway VPN è necessario per distribuire un partner di sicurezza attendibile nell'hub. 
6. Selezionare **Avanti: firewall di Azure**
   > [!NOTE]
   > Partner di sicurezza attendibili si connettono all'hub usando i tunnel del gateway VPN. Se si elimina il gateway VPN, le connessioni ai partner di sicurezza attendibili andranno perse.
7. Se si vuole distribuire il firewall di Azure per filtrare il traffico privato insieme al provider di servizi di terze parti per filtrare il traffico Internet, selezionare un criterio per il firewall di Azure. Vedere gli [scenari supportati](trusted-security-partners.md#key-scenarios).
8. Se si vuole distribuire solo un provider di sicurezza di terze parti nell'hub, selezionare **Azure firewall: Enabled/Disabled** per impostarlo su **Disabled (disabilitato**). 
9. Selezionare **Avanti: partner di sicurezza attendibili**.
10. Selezionare **Trusted Security partner** per impostarlo su **abilitato**. Selezionare un partner. 
11. Selezionare **Avanti**. 
12. Esaminare il contenuto e quindi selezionare **Crea**.

La distribuzione del gateway VPN può richiedere più di 30 minuti.

Per verificare che l'hub sia stato creato, passare ad Azure Firewall Manager-> Hub protetti. Selezionare la pagina panoramica Hub-> per visualizzare il nome del partner e lo stato come **connessione di sicurezza in sospeso**.

Dopo la creazione dell'hub e la configurazione del partner di sicurezza, continuare con per connettere il provider di sicurezza all'hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuire un provider di sicurezza di terze parti in un hub esistente

È anche possibile selezionare un hub esistente in una rete WAN virtuale e convertirlo in un *hub virtuale protetto*.

1. In **Introduzione**selezionare **Converti Hub esistenti**.
2. Selezionare una sottoscrizione e un hub esistente. Seguire i passaggi rimanenti per distribuire un provider di terze parti in un nuovo hub.

Tenere presente che è necessario distribuire un gateway VPN per convertire un hub esistente nell'hub protetto con provider di terze parti.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurare provider di sicurezza di terze parti per la connessione a un hub protetto

Per configurare i tunnel per il gateway VPN dell'hub virtuale, i provider di terze parti necessitano dei diritti di accesso all'hub. A tale scopo, associare un'entità servizio alla sottoscrizione o al gruppo di risorse e concedere i diritti di accesso. È quindi necessario assegnare queste credenziali a terze parti tramite il portale.

1. Creare un'entità servizio Azure Active Directory (AD): è possibile ignorare l'URL di reindirizzamento. 

   [Procedura: usare il portale per creare un'applicazione Azure AD e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Aggiungere i diritti di accesso e l'ambito per l'entità servizio.
   [Procedura: usare il portale per creare un'applicazione Azure AD e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > È possibile limitare l'accesso solo al gruppo di risorse per un controllo più granulare.
3. Seguire le istruzioni riportate in [ZScaler: Configuring a Microsoft Azure Virtual WAN Integration](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) to:

   - Accedere al portale per i partner e aggiungere le credenziali per consentire al partner attendibile di accedere all'hub protetto.
   - Sincronizzare gli hub virtuali nel portale per i partner e configurare il tunnel per l'hub virtuale. Questa operazione può essere eseguita dopo la convalida delle credenziali di autenticazione Azure AD.
   
4. È possibile esaminare lo stato di creazione del tunnel nel portale WAN virtuale di Azure in Azure. Quando i tunnel sono **connessi** sia in Azure che nel portale per i partner, continuare con i passaggi successivi per configurare le route per selezionare quali Branch e reti virtuali devono inviare il traffico Internet al partner.

## <a name="configure-route-settings"></a>Configurare le impostazioni della route

1. Passare ad Azure Firewall Manager-> Hub protetti. 
2. Selezionare un hub. Lo stato dell'hub dovrebbe ora visualizzare il **provisioning** invece della **connessione di sicurezza in sospeso**.

   Verificare che il provider di terze parti possa connettersi all'hub. I tunnel nel gateway VPN devono trovarsi in uno stato **connesso** . Questo stato è più riflettente sull'integrità della connessione tra l'hub e il partner di terze parti rispetto allo stato precedente.
3. Selezionare l'hub e passare a **Impostazioni route**.

   Quando si distribuisce un provider di terze parti nell'hub, l'hub viene convertito in un *hub virtuale protetto*. Ciò garantisce che il provider di terze parti stia annunciando una route 0.0.0.0/0 (impostazione predefinita) all'hub. Tuttavia, le connessioni VNet e i siti connessi all'hub non ottengono questa route, a meno che non si scelga esplicitamente le connessioni che devono ottenere la route predefinita.
4. In **traffico Internet**selezionare **da VNet a Internet** o da **ramo a Internet** oppure entrambe le route sono configurate invia tramite la terza parte.

   Questo indica solo il tipo di traffico che deve essere indirizzato all'hub, ma non influisce sulle route su reti virtuali o sui rami. Queste route non vengono propagate a tutti i reti virtuali/rami collegati all'hub per impostazione predefinita.
5. È necessario selezionare **connessioni sicure** e selezionare le connessioni in cui devono essere impostate tali route. Ciò indica quali reti virtuali/Branch possono iniziare a inviare traffico Internet al provider di terze parti.
6. In **Impostazioni route**selezionare **connessioni sicure** in traffico Internet, quindi selezionare VNet o Branch (*siti* in WAN virtuale) da proteggere. Selezionare **traffico Internet sicuro**.
   ![il traffico Internet sicuro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Tornare alla pagina Hub. Lo stato del **partner di sicurezza attendibile** dell'hub dovrebbe essere ora **protetto**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Ramo o traffico Internet VNet tramite servizio di terze parti

Successivamente, è possibile verificare se le macchine virtuali VNet o il sito di succursale può accedere a Internet e verificare che il traffico venga propagato al servizio di terze parti.

Dopo aver completato i passaggi di impostazione della route, le macchine virtuali VNet e i siti secondari vengono inviati a 0/0 una route del servizio di terze parti. Non è possibile usare RDP o SSH in queste macchine virtuali. Per accedere, è possibile distribuire il servizio [Azure Bastion](../bastion/bastion-overview.md) in un VNet con peering.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: proteggere la rete cloud con Azure Firewall Manager Preview usando il portale di Azure](secure-cloud-network.md)




