---
title: Distribuire un servizio cloud in un ambiente di gestione temporanea (Node.js) | Documentazione Microsoft
description: Informazioni su come distribuire l'applicazione di Azure prima in un ambiente di gestione temporanea e quindi in un ambiente di produzione tramite uno scambio di IP virtuali (VIP, Virtual IP).
services: cloud-services
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: a015c4a2f5dccb8cae49b739e5d8c342daec54cf
ms.contentlocale: it-it
ms.lasthandoff: 01/05/2017

---
# <a name="staging-an-application-in-azure"></a>Gestione temporanea di un'applicazione in Azure
È possibile distribuire nell'ambiente di gestione temporanea di Azure un'applicazione inclusa in un pacchetto per la verifica prima dello spostamento nell'ambiente di produzione in cui l'applicazione risulterà accessibile su Internet. L'ambiente di gestione temporanea è identico all'ambiente di produzione, ma è possibile accedere all'applicazione di gestione temporanea solo tramite un URL offuscato generato da Azure. Dopo la verifica del funzionamento corretto dell'applicazione, sarà possibile distribuirla nell'ambiente di produzione, tramite uno scambio di indirizzi IP virtuali (VIP, Virtual IP).

> [!NOTE]
> I passaggi illustrati in questo articolo riguardano esclusivamente le applicazioni Node ospitate come Servizio cloud di Azure.
> 
> 

## <a name="step-1-stage-an-application"></a>Passaggio 1: Gestire un'applicazione in modo temporaneo
In questa attività viene illustrato come gestire un'applicazione in modo temporaneo usando **Microsoft Azure PowerShell**.

1. Quando si pubblica un servizio, è sufficiente passare il parametro **-Slot** al cmdlet **Publish-AzureServiceProject**.
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. Accedere al [portale di Azure classico] e selezionare **Servizi cloud**. Dopo la creazione del servizio cloud e l'aggiornamento dello stato della colonna **Staging** a **In esecuzione**, fare clic sul nome del servizio.
   
   ![Portale con servizio in esecuzione][cloud-service]
3. Selezionare **Dashboard** e quindi **Staging**.
   
   ![Dashboard del servizio cloud][cloud-service-dashboard]
4. Si noti il valore nella voce **Site URL** a destra. Il nome DNS è un ID interno offuscato generato da Azure.
   
    ![Site URL][cloud-service-staging-url]

È ora possibile verificare il corretto funzionamento dell'applicazione nell'ambiente di gestione temporanea, usando l'URL del sito di gestione temporanea.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Passaggio 2: Aggiornare un'applicazione in produzione tramite lo scambio di indirizzi VIP
Dopo la verifica della versione aggiornata di un'applicazione nell'ambiente di gestione temporanea, sarà possibile renderla rapidamente disponibile in produzione tramite lo scambio degli indirizzi IP virtuali (VIP, Virtual IP) degli ambienti di gestione temporanea e di produzione.

> [!NOTE]
> In questo passaggio si presuppone che un'applicazione sia già stata distribuita in produzione e che sia già stata eseguita la gestione temporanea della versione aggiornata dell'applicazione.
> 
> 

1. Accedere al [portale di Azure classico], fare clic su **Servizi Cloud** e quindi selezionare il nome del servizio.
2. In **Dashboard** selezionare **Staging** e quindi fare clic su **Scambia** nella parte inferiore della pagina. Verrà aperta la finestra di dialogo VIP Swap.
   
   ![Finestra di dialogo VIP Swap][vip-swap-dialog]
3. Verificare le informazioni e quindi fare clic su **OK**. Verrà avviato l'aggiornamento delle due distribuzioni, mentre la distribuzione di gestione temporanea passa in produzione e la distribuzione di produzione passa in gestione temporanea.

La gestione temporanea di una distribuzione e l'aggiornamento di una distribuzione di produzione sono stati eseguiti correttamente tramite lo scambio di indirizzi IP virtuali con la distribuzione in gestione temporanea.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Come distribuire un aggiornamento del servizio in produzione mediante lo scambio di indirizzi VIP in Azure]

[portale di Azure classico]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Come distribuire un aggiornamento del servizio in produzione mediante lo scambio di indirizzi VIP in Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production

