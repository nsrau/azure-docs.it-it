---
title: Abilitare FTP nel servizio app in Azure Stack | Documentazione Microsoft
description: Passaggi da completare per abilitare FTP nel servizio app in Azure Stack
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Abilitare FTP nel servizio app in Azure Stack

Se si vuole abilitare la pubblicazione FTP in modo che i tenant possano caricare il contenuto e i file dell'applicazione dopo avere distribuito il servizio app in Azure Stack, è necessario completare alcuni passaggi aggiuntivi.  Nelle versioni future questi passaggi verranno automatizzati.

> [!NOTE]
> Questi passaggi sono relativi agli amministratori dell'organizzazione o del servizio che configurano un servizio app nel provider di risorse di Azure Stack.

## <a name="enable-ftp"></a>Abilitare FTP

1.  Accedere al portale di Azure Stack come amministratore del servizio.
2.  Passare a **Interfacce di rete** e selezionare **FTP-NIC** in **Gruppo di risorse** - **AppService-LOCAL**. ![Interfacce di rete di Azure Stack][1]
3.  Annotare l'**indirizzo IP pubblico** di **FTP-NIC**. 
![Dettagli delle interfacce di rete di Azure Stack][2]
4.  Passare quindi a **macchine virtuali** e selezionare **FTP0-VM**. ![Macchine virtuali di Azure Stack][3]
5.  Aprire una sessione di desktop remoto per la VM usando il pulsante **Connetti** e accedere alla sessione usando le credenziali di amministratore impostate durante la distribuzione del servizio app.  
![Dettagli delle macchine virtuali di Azure Stack][4]
6.  Aprire **Internet Information Services (IIS)** nella VM FTP (FTP0-VM).
7.  In **Siti** selezionare **Hosting FTP Site** (Sito FTP hosting).
8.  Aprire **Supporto firewall FTP**. ![Gestione IIS in servizio App FTP0-VM][5]
9.  Immettere l'indirizzo IP pubblico di FTP-NIC e fare clic su **Applica** ![Supporto Firewall FTP di Gestione IIS][6]

## <a name="validate-the-enabling-of-ftp"></a>Convalidare l'abilitazione di FTP

1.  Accedere al portale di Azure Stack come amministratore del servizio o come tenant.
2.  Passare a **Servizi App** e selezionare l'app per le API, dispositivi mobili o Web creata. ![Servizi app][7]
3.  Nei dettagli dell'applicazione annotare il **nome host FTP** e il **nome utente FTP/distribuzione**. ![Dettagli dell'applicazione del servizio app][8]
> [!NOTE]
> Se non è presente una voce nella sezione **nome utente FTP/distribuzione**, è necessario impostare prima le credenziali di distribuzione usando il pannello **Credenziali di distribuzione**.

4.  Aprire Esplora risorse, immettere il nome host FTP nella barra degli indirizzi file, ad esempio ftp://ftp.appservice.azurestack.local
5.  Quando richiesto immettere le **credenziali di distribuzione** annotate nel passaggio 3, se la funzione è stata abilitata verrà visualizzato un elenco di directory del contenuto dell'applicazione del servizio app. ![Elenco di file FTP][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
