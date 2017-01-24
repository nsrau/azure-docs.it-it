---
title: Clonazione di app Web con il portale di Azure
description: Come clonare le app Web in nuove app Web con il portale di Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 9ebfa91c7972ab3c264032ead8376c23c1197a4b


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Clonazione di app del servizio app di Azure con il portale di Azure
La funzionalità di clonazione nelle [app Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) consente facilmente di clonare app Web esistenti in un'app appena creata in un'area diversa o nella stessa area. In questo modo i clienti possono distribuire una quantità di app in aree geografiche diverse rapidamente e con facilità.

La clonazione di app è attualmente supportata solo per i piani di servizio app Premium. La nuova funzionalità usa le stesse limitazioni della funzionalità di backup di App Web. Vedere [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonazione di un'app esistente
L'app Web deve essere in esecuzione in modalità **Premium** per poter creare un clone per l'app Web.

1. Nel [portale di Azure](https://portal.azure.com/), aprire il pannello dell'app Web.
2. Fare clic su **Strumenti**. Quindi, nel pannello **Strumenti** selezionare **Clona app**.
   
    ![][1]
   
   > [!NOTE]
   > Se l'app Web non è già in modalità **Standard** , si riceverà un messaggio che indica le modalità supportate per la clonazione dell'app. A questo punto è possibile selezionare **Aggiorna**.
   > 
   > 
3. Nel pannello **Clona app** specificare un nome per la nuova app Web, il gruppo di risorse e il piano di servizio app. Inoltre, l'utente sarà in grado di scegliere se clonare un numero di impostazioni dell'app Web di origine o meno.
   
    ![][2]
4. Dopo aver selezionato **Crea** , la piattaforma inizierà a creare un clone per l'app Web di origine.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonazione di un'app esistente in un ambiente del servizio App
Nel pannello **Clona app** il cliente ha la possibilità di selezionare un pool di app in un ambiente del servizio app esistente.

## <a name="current-restrictions"></a>Restrizioni attuali
Questa funzionalità è attualmente in anteprima e sono allo studio nuove funzionalità che saranno aggiunte con il tempo. L'elenco seguente include le restrizioni note riguardanti l'attuale supporto per la clonazione di app nel portale di Azure:

* Le impostazioni di Gestione traffico di Azure non vengono clonate.
* Le impostazioni di ridimensionamento automatico non vengono clonate.
* Le impostazioni di pianificazione del backup non vengono clonate.
* Le impostazioni della rete virtuale non vengono clonate.
* Le informazioni sull'app non vengono configurate automaticamente nell'app Web di destinazione.
* Le impostazioni di Easy Auth non vengono clonate.
* L'estensione Kudu non viene clonata.
* Le regole TiP non vengono clonate.
* Il contenuto di database non viene clonato.

### <a name="references"></a>Riferimenti
* [Clonazione di app Web con PowerShell](app-service-web-app-cloning.md)
* [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md)
* [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md)
* [Creare un'app Web in un ambiente del servizio app](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png



<!--HONumber=Dec16_HO2-->


