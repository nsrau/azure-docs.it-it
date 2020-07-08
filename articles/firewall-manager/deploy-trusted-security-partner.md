---
title: Distribuire un provider del partner di sicurezza di Azure Firewall Manager
description: Informazioni su come distribuire un provider del partner di sicurezza di Azure Firewall Manager usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3323f73c137905fbe677c68d3830d7f609fa0172
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611578"
---
# <a name="deploy-a-security-partner-provider"></a>Distribuire un provider del partner di sicurezza

I *provider di partner di sicurezza* in gestione firewall di Azure consentono di usare le proprie offerte di sicurezza come servizio (secar) di terze parti per proteggere l'accesso a Internet per gli utenti.

Per altre informazioni sugli scenari supportati e sulle procedure consigliate, vedere [che cosa sono i provider di partner di sicurezza?](trusted-security-partners.md)


I partner integrati di terze parti per la sicurezza come servizio (SECas) sono ora disponibili in tutte le aree del cloud pubblico di Azure. L'integrazione di **zScaler** sarà disponibile a livello generale il 3 luglio 2020. **Check Point** è un partner Secas supportato e sarà in anteprima il 3 luglio 2020. l'integrazione di **iboss** sarà disponibile a livello generale il 31 luglio 2020.

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuire un provider di sicurezza di terze parti in un nuovo hub

Ignorare questa sezione se si distribuisce un provider di terze parti in un hub esistente.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella casella di **ricerca**digitare **Firewall Manager** e selezionarlo in **Servizi**.
3. Passare a **Introduzione**. Selezionare **Visualizza Hub virtuali protetti**.
4. Selezionare **Crea nuovo hub virtuale protetto**.
5. Immettere la sottoscrizione e il gruppo di risorse, selezionare un'area supportata e aggiungere le informazioni sull'hub e sulla rete WAN virtuale. 
6. Selezionare **Includi gateway VPN per abilitare i provider del partner di sicurezza**.
7. Selezionare le **unità di scala del gateway** appropriate per le proprie esigenze.
8. Selezionare **Avanti: firewall di Azure**
   > [!NOTE]
   > I provider di partner di sicurezza si connettono all'hub usando i tunnel del gateway VPN. Se si elimina il gateway VPN, le connessioni ai provider del partner di sicurezza andranno perse.
9. Se si vuole distribuire il firewall di Azure per filtrare il traffico privato insieme al provider di servizi di terze parti per filtrare il traffico Internet, selezionare un criterio per il firewall di Azure. Vedere gli [scenari supportati](trusted-security-partners.md#key-scenarios).
10. Se si vuole distribuire solo un provider di sicurezza di terze parti nell'hub, selezionare **Azure firewall: Enabled/Disabled** per impostarlo su **Disabled (disabilitato**). 
11. Fare clic su **Next: Security partner provider**.
12. Impostare **provider del partner di sicurezza** su **abilitato**. 
13. Selezionare un partner. 
14. Selezionare **Avanti: Rivedi e crea**. 
15. Esaminare il contenuto e quindi selezionare **Crea**.

La distribuzione del gateway VPN può richiedere più di 30 minuti.

Per verificare che l'hub sia stato creato, passare ad Azure Firewall Manager->Hub protetti. Selezionare la pagina panoramica Hub->per visualizzare il nome del partner e lo stato come **connessione di sicurezza in sospeso**.

Dopo la creazione dell'hub e la configurazione del partner di sicurezza, continuare con per connettere il provider di sicurezza all'hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuire un provider di sicurezza di terze parti in un hub esistente

È anche possibile selezionare un hub esistente in una rete WAN virtuale e convertirlo in un *hub virtuale protetto*.

1. In **Introduzione**selezionare **Visualizza Hub virtuali protetti**.
2. Selezionare **Converti Hub esistenti**.
3. Selezionare una sottoscrizione e un hub esistente. Seguire i passaggi rimanenti per distribuire un provider di terze parti in un nuovo hub.

Tenere presente che è necessario distribuire un gateway VPN per convertire un hub esistente nell'hub protetto con provider di terze parti.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurare provider di sicurezza di terze parti per la connessione a un hub protetto

Per configurare i tunnel per il gateway VPN dell'hub virtuale, i provider di terze parti necessitano dei diritti di accesso all'hub. A tale scopo, associare un'entità servizio alla sottoscrizione o al gruppo di risorse e concedere i diritti di accesso. È quindi necessario assegnare queste credenziali a terze parti tramite il portale.

### <a name="create-and-authorize-a-service-principal"></a>Creare e autorizzare un'entità servizio

1. Creare un'entità servizio Azure Active Directory (AD): è possibile ignorare l'URL di reindirizzamento. 

   [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Aggiungere i diritti di accesso e l'ambito per l'entità servizio.
   [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > È possibile limitare l'accesso solo al gruppo di risorse per un controllo più granulare.

### <a name="visit-partner-portal"></a>Visita il portale per i partner

1. Per completare l'installazione, seguire le istruzioni fornite dal partner. Questo include l'invio di informazioni di AAD per rilevare e connettersi all'hub, aggiornare i criteri in uscita e controllare lo stato e i log di connettività.

   - [ZScaler: configurare Microsoft Azure integrazione WAN virtuale](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point (anteprima): configurare Microsoft Azure l'integrazione WAN virtuale](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss (anteprima): configurare Microsoft Azure integrazione WAN virtuale](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. È possibile esaminare lo stato di creazione del tunnel nel portale WAN virtuale di Azure in Azure. Quando i tunnel sono **connessi** sia in Azure che nel portale per i partner, continuare con i passaggi successivi per configurare le route per selezionare quali Branch e reti virtuali devono inviare il traffico Internet al partner.

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
   ![Traffico Internet sicuro](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Tornare alla pagina Hub. Lo stato del **provider del partner di sicurezza** dell'hub dovrebbe essere ora **protetto**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Ramo o traffico Internet VNet tramite servizio di terze parti

Successivamente, è possibile verificare se le macchine virtuali VNet o il sito di succursale può accedere a Internet e verificare che il traffico venga propagato al servizio di terze parti.

Dopo aver completato i passaggi di impostazione della route, le macchine virtuali VNet e i siti secondari vengono inviati a 0/0 una route del servizio di terze parti. Non è possibile usare RDP o SSH in queste macchine virtuali. Per accedere, è possibile distribuire il servizio [Azure Bastion](../bastion/bastion-overview.md) in un VNet con peering.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: proteggere la rete cloud con Azure Firewall Manager usando il portale di Azure](secure-cloud-network.md)