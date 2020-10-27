---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - cita un problema noto con l'autenticazione di Cloud Shell
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92323236"
---
>[!NOTE]
>Esiste attualmente un **problema noto** in Cloud Shell che interessa questi gruppi di comandi quando eseguiti da *https://shell.azure.com* : `az dt route`, `az dt model`, `az dt twin`.
>
>Per risolvere il problema, provare una delle operazioni seguenti:
> * Eseguire `az login` in Cloud Shell prima di eseguire il comando.
> * Aprire il riquadro di Cloud Shell nel portale di Azure e completare da lì le operazioni di Cloud Shell.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Visualizzazione del portale di Azure con l'icona Cloud Shell evidenziata e il riquadro di Cloud Shell visualizzato nella parte inferiore della finestra del portale":::
> * Usare l'[interfaccia della riga di comando locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) al posto di Cloud Shell.
>
>Per informazioni dettagliate su questo problema, vedere [*Risoluzione dei problemi: problemi noti in Gemelli digitali di Azure*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).