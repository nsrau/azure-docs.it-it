---
title: Installare il gateway dati locale per Azure Analysis Services | Microsoft Docs
description: Informazioni su come installare e configurare un gateway dati locale.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 99b5a0ff9cc0fd7fdc3b8a0e453ed287c45e1c60
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147117"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installare e configurare un gateway dati locale

Quando uno o più server Azure Analysis Services nella stessa area si connettono a origini dati locali, è necessario un gateway dati locale.  Mentre il gateway installato è lo stesso usato da altri servizi, ad esempio Power BI, Power Apps e app per la logica, quando si installa per Azure Analysis Services, è necessario completare alcuni passaggi aggiuntivi. Questo articolo di installazione è specifico per **Azure Analysis Services**.

Per altre informazioni sul gateway e su come viene usato da Azure Analysis Services, vedere [connessione alle origini dati locali](analysis-services-gateway.md).

## <a name="prerequisites"></a>Prerequisiti

**Requisiti minimi:**

* .NET Framework 4.5
* Versione a 64 bit di Windows 7 o Windows Server 2008 R2 (o versione successiva)

**Consigliato:**

* 8 CPU core
* 8 GB di memoria
* Windows 2012 R2 versione a 64 bit (o versione successiva)

**Considerazioni importanti:**

* Durante l'installazione, quando si registra il gateway con Azure, viene selezionata l'area predefinita per la sottoscrizione. È possibile scegliere un'area diversa. Se si dispone di server in più aree, è necessario installare un gateway per ciascuna area. 
* Il gateway non può essere installato in un controller di dominio.
* In un singolo computer può essere installato un solo gateway.
* Installare il gateway in un computer che rimane attivo e non passa alla modalità di sospensione.
* Non installare il gateway in un computer connesso alla rete in modalità wireless. È infatti possibile che si verifichi un calo delle prestazioni.
* Quando si installa il gateway, l'account utente con il quale è stato effettuato l'accesso al computer deve accedere come privilegi del servizio. Al termine dell'installazione, il servizio di gateway dati locale usa l'account NT SERVICE\PBIEgwService per accedere come un servizio. È possibile specificare un altro account durante l'installazione o in Services al termine dell'installazione. Assicurarsi che le impostazioni dei Criteri di gruppo consentano di accedere come privilegi di servizio all'account con il quale ci si è connessi al momento dell'installazione e all'account di servizio scelto per l'accesso.
* Accedere ad Azure con un account di Azure AD per lo stesso [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) della sottoscrizione in cui si sta registrando il gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione di un gateway.
* Se le origini dati si trovano in un'istanza di Rete virtuale di Azure (VNet), è necessario configurare la proprietà del server [AlwaysUseGateway](analysis-services-vnet-gateway.md).
* Il gateway (unificato) qui descritto non è supportato nelle aree di Azure Germania. Usare invece il **gateway locale dedicato per Azure Analysis Services**, installato da **Avvio rapido** del server nel portale. 


## <a name="download"></a>Scaricare

 [Scaricare il gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installare

1. Eseguire l'installazione.

2. Selezionare **gateway dati locale**.

   ![Seleziona](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selezionare un percorso, accettare le condizioni e quindi fare clic su **Installa**.

   ![Percorso di installazione e condizioni di licenza](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Accedere ad Azure. L'account deve essere nell'istanza di Microsoft Azure Active Directory del tenant. Questo account viene usato per l'amministratore del gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione del gateway.

   ![Accedere a Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se si accede con un account di dominio, questo viene mappato all'account aziendale in Azure AD. L'account aziendale viene usato come amministratore del gateway.

## <a name="register"></a>Registrare

Per creare una risorsa per il gateway in Azure, è necessario registrare l'istanza locale installata con il servizio cloud gateway. 

1.  Selezionare l'opzione **Consente di registrare un nuovo gateway in questo computer**.

    ![Iscriviti](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digitare un nome e la chiave di ripristino per il gateway. Per impostazione predefinita, il gateway usa l'area predefinita della sottoscrizione. Se è necessario selezionare un'area diversa, selezionare **Cambia area**.

    > [!IMPORTANT]
    > Salvare la chiave di ripristino in un luogo sicuro. La chiave di ripristino è necessaria per poter acquisire la proprietà, eseguire la migrazione o ripristinare un gateway. 

   ![Iscriviti](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Creare una risorsa per il gateway di Azure

Dopo aver installato e registrato il gateway, è necessario creare una risorsa per il gateway nella sottoscrizione di Azure. Accedere ad Azure con lo stesso account usato durante la registrazione del gateway.

1. In portale di Azure fare clic su **Crea una risorsa**, quindi cercare **il gateway dati locale**e quindi fare clic su **Crea**.

   ![Creazione di una risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Crea gateway di connessione** immettere queste impostazioni:

   * **Nome**: inserire un nome per la risorsa del gateway. 

   * **Sottoscrizione**: selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
   
     La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

   * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente.

   * **Percorso**: selezionare l'area in cui è stato registrato il gateway.

   * **Nome installazione**: se l'installazione del gateway non è già selezionata, selezionare il gateway installato nel computer e registrato. 

     Al termine dell'operazione, scegliere **Crea**.

## <a name="connect-servers"></a>Connettere i server alla risorsa per il gateway

1. Nella panoramica del server Azure Analysis Services fare clic su **Gateway dati locale**.

   ![Connessione del server al gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. In **Selezionare un gateway dati locale da connettere** selezionare la risorsa per il gateway e quindi fare clic su **Connetti gateway selezionato**.

   ![Connessione del server alla risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se il gateway non viene visualizzato nell'elenco, probabilmente il server non si trova nell'area specificata durante la registrazione del gateway.

    Quando la connessione tra il server e la risorsa del gateway ha esito positivo, lo stato visualizzerà **Connected**.


    ![Connessione del server alla risorsa del gateway riuscita](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Ecco fatto. Per aprire porte o risolvere qualsiasi problema, assicurarsi di consultare [Gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire Analysis Services](analysis-services-manage.md)   
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)   
* [Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure](analysis-services-vnet-gateway.md)
