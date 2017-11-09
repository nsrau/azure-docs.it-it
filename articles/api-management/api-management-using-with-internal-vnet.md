---
title: Come usare Gestione API di Azure con le reti virtuali interne | Microsoft Docs
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna.
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: 2a496059d1959a6c9e762e70dfbeff9bf961c4d4
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Uso del servizio Gestione API di Azure con una rete virtuale interna
Grazie alle reti virtuali di Azure, Gestione API è in grado di gestire API non accessibili su Internet. Sono disponibili varie tecnologie VPN per stabilire la connessione. È possibile distribuire Gestione API in due modalità principali all'interno di una rete virtuale:
* Esterno
* Interno


Quando il servizio Gestione API viene installato in modalità di rete virtuale interna, tutti gli endpoint di servizio, ovvero gateway, portale per sviluppatori, portale di pubblicazione, gestione diretta e GIT, sono visibili solo all'interno di una rete virtuale i cui accessi sono gestiti dall'utente. Nessuno degli endpoint di servizio è registrato nel server DNS pubblico.

Usando Gestione API in modalità interna è possibile implementare gli scenari seguenti:
* Consentire un accesso esterno sicuro da parte di terzi alle API ospitate in un data center privato, tramite connessioni VPN da sito a sito o Azure ExpressRoute.
* Abilitare scenari cloud ibridi esponendo le API basate su cloud e locali tramite un gateway comune.
* Gestire le API ospitate in più aree geografiche usando un singolo endpoint del gateway. 


## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ **Una sottoscrizione di Azure attiva**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Un'istanza di Gestione API**. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

## <a name="enable-vpn"> </a>Creazione di un servizio Gestione API in una rete virtuale interna
Il servizio Gestione API in una rete virtuale interna viene ospitato dietro un servizio di bilanciamento del carico interno (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Abilitare la connessione a una rete virtuale usando il portale di Azure

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com/).
2. Selezionare **Domini e SSL personalizzati**.
3. Configurare l'istanza di Gestione API da distribuire all'interno della rete virtuale.

    ![Menu per la configurazione di Gestione API di Azure in una rete virtuale interna][api-management-using-internal-vnet-menu]

4. Selezionare **Salva**.

Una volta completata la distribuzione, nel dashboard dovrebbe essere visualizzato l'indirizzo IP virtuale interno del servizio.

![Dashboard di Gestione API con una rete virtuale interna configurata][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Abilitare una connessione di rete virtuale tramite i cmdlet di PowerShell
È possibile abilitare la connettività a una rete virtuale anche usando i cmdlet di PowerShell.

* Creare un servizio Gestione API all'interno di una rete virtuale: usare il cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) per creare un servizio Gestione API di Azure all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

* Distribuire un servizio Gestione API esistente all'interno di una rete virtuale: usare il cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) per spostare un servizio Gestione API di Azure all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

## <a name="apim-dns-configuration"></a>Configurazione del DNS
Quando si usa Gestione API in modalità di rete virtuale esterna, il servizio DNS è gestito da Azure. Per la modalità di rete virtuale interna, è necessario gestire il routing proprio.

> [!NOTE]
> Il servizio Gestione API non ascolta le richieste in arrivo dagli indirizzi IP. Risponde solo alle richieste per il nome host configurato negli endpoint di servizio. Questi endpoint includono gateway, portale per sviluppatori, portale di pubblicazione, endpoint di gestione diretta e GIT.

### <a name="access-on-default-host-names"></a>Accesso con i nomi host predefiniti
Quando si crea un servizio Gestione API, denominato ad esempio "contoso", vengono configurati i seguenti endpoint di servizio per impostazione predefinita:

   * Gateway o proxy: contoso.azure-api.net

   * Portale di pubblicazione e portale per sviluppatori: contoso.portal.azure-api.net

   * Endpoint di gestione diretta: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Per accedere a questi endpoint del servizio Gestione API è possibile creare una macchina virtuale in una subnet connessa alla rete virtuale in cui viene distribuito Gestione API. Supponendo che l'indirizzo IP virtuale interno per il servizio sia 10.0.0.5, è possibile eseguire il mapping del file hosts, %SystemDrive%\drivers\etc\hosts, come segue:

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

È quindi possibile accedere a tutti gli endpoint di servizio dalla macchina virtuale creata. Se si usa un server DNS personalizzato in una rete virtuale, è anche possibile creare record DNS A e accedere a questi endpoint da qualsiasi punto nella rete virtuale. 

### <a name="access-on-custom-domain-names"></a>Accesso con i nomi di dominio personalizzati

   1. Se non si vuole accedere al servizio Gestione API con i nomi host predefiniti, è possibile configurare nomi di dominio personalizzati per tutti gli endpoint di servizio come mostrato nella figura seguente: 

   ![Configurazione di un dominio personalizzato per Gestione API][api-management-custom-domain-name]

   2. È quindi possibile creare record nel server DNS per accedere agli endpoint accessibili solo dall'interno della rete virtuale.

## <a name="routing"> </a> Routing
+ Verrà riservato e usato un indirizzo IP virtuale privato con bilanciamento del carico dall'intervallo della subnet per accedere agli endpoint del servizio Gestione API dall'interno della rete virtuale.
+ Verrà inoltre riservato un indirizzo IP pubblico (VIP) con bilanciamento del carico per fornire l'accesso all'endpoint del servizio di gestione solo sulla porta 3443.
+ Verranno usati un indirizzo IP di un intervallo IP di subnet (DIP) per accedere alle risorse all'interno della rete rituale e un indirizzo IP pubblico (VIP) per accedere alle risorse esterne alla rete virtuale.
+ Gli indirizzi IP pubblici e privati con bilanciamento del carico sono disponibili nel pannello Panoramica/Informazioni di base del portale di Azure.

## <a name="related-content"></a>Contenuti correlati
Per altre informazioni, vedere gli articoli seguenti:
* [Problemi comuni di configurazione di rete durante la configurazione di Gestione API di Azure in una rete virtuale][Common network configuration problems]
* [Domande frequenti sulla rete virtuale di Azure](../virtual-network/virtual-networks-faq.md)
* [Creazione di un record in DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png


[Create API Management service]: api-management-get-started.md#create-service-instance
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

