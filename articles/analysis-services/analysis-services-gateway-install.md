---
title: Installare il gateway dati locale | Microsoft Docs
description: Informazioni su come installare e configurare un gateway dati locale.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/22/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6ef296fb98478be9240f0231c8ad39cd2a0af995
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installare e configurare un gateway dati locale
Quando uno o più server Azure Analysis Services nella stessa area si connettono a origini dati locali, è necessario un gateway dati locale. Per ulteriori informazioni sul gateway, vedere [Gateway dati locale](analysis-services-gateway.md).

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


## <a name="download"></a>Download
 [Scaricare il gateway](https://aka.ms/azureasgateway)

## <a name="install"></a>Installare

1. Eseguire l'installazione.

2. Selezionare un percorso, accettare le condizioni e quindi fare clic su **Installa**.

   ![Percorso di installazione e condizioni di licenza](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Selezionare **Gateway dati locale (scelta consigliata)**. Azure Analysis Services non supporta la modalità personale.

   ![Scegliere il tipo di gateway](media/analysis-services-gateway-install/aas-gateway-installer-shared.png)

4. Immettere un account per accedere ad Azure. L'account deve essere nella Microsift Azure Active Directory del tenant. Questo account viene utilizzato per l'amministratore del gateway. 

   ![Immettere un account per accedere ad Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se si accede con un account di dominio, esso verrà mappato all'account aziendale in Azure AD. L'account aziendale verrà usato come l'amministratore del gateway.

## <a name="register"></a>Registrare
Per creare una risorsa per il gateway in Azure, è necessario registrare l'istanza locale installata con il servizio Cloud Gateway. 

1.  Selezionare l'opzione che **consente di registrare un nuovo gateway in questo computer**.

    ![Registra](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digitare un nome e la chiave di ripristino per il gateway. Per impostazione predefinita, il gateway utilizza l'area predefinita della sottoscrizione. Se è necessario selezionare un'area diversa, selezionare **Modifica area**.

   ![Registra](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Creare una risorsa per il gateway di Azure
Dopo aver installato e registrato il gateway, è necessario creare una risorsa per il gateway nella sottoscrizione di Azure. Accedere ad Azure con lo stesso account utilizzato quando si registra il gateway.

1. Nel portale di Azure, fare clic su **Creare un nuovo servizio** > **Integrazione Enterprise**  > **Gateway dati locale** > **Crea**.

   ![Creare una risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Crea gateway di connessione**, immettere queste impostazioni:

    * **Nome**: inserire un nome per la risorsa del gateway. 

    * **Sottoscrizione**: selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
    La presente sottoscrizione deve essere la medesima in cui si trovano i server.
   
      La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

    * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente.

    * **Percorso**: selezionare l'area in cui è stato registrato il gateway.

    * **Nome installazione**: se l'installazione del gateway non è già selezionata, selezionare il gateway registrato. 

    Al termine dell'operazione, scegliere **Crea**.

## <a name="connect-servers"></a>Connettere i server alla risorsa per il gateway

1. Nella panoramica del server Azure Analysis Services, fare clic su **Gateway dati locale**.

   ![Connettere il server al gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. In **Selezionare un Gateway dati locale per la connessione**, selezionare la risorsa per il gateway e quindi fare clic su **Connetti gateway selezionato**.

   ![Connettere il server alla risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se il gateway non viene visualizzato nell'elenco, probabilmente il server non si trova nella stessa area specificata durante la registrazione del gateway. 

È tutto. Se è necessario aprire le porte o eseguire qualsiasi risoluzione dei problemi, assicurarsi di eseguire il check oud del [gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi
* [Gestire Analysis Services](analysis-services-manage.md)   
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)

