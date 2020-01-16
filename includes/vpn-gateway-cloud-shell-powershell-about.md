---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045104"
---
Questo articolo usa i cmdlet di PowerShell. Per eseguire i cmdlet è possibile usare Azure Cloud Shell, un ambiente di shell interattiva ospitato in Azure e usato tramite il browser. In Azure Cloud Shell sono preinstallati i cmdlet di Azure PowerShell.

Per eseguire il codice contenuto in questo articolo in Azure Cloud Shell, aprire una sessione di Cloud Shell, usare il pulsante **Copia** in un blocco di codice per copiare il codice e incollarlo nella sessione di Cloud Shell con __CTRL+MAIUSC+V__ in Windows e Linux o __CMD+MAIUSC+V__ in macOS. Il testo incollato non viene eseguito automaticamente. Premere quindi **INVIO** per eseguire il codice.

È possibile avviare Azure Cloud Shell nei modi seguenti:

|  |   |
|-----------------------------------------------|---|
| Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. Questa azione __non__ copia automaticamente il testo in Cloud Shell. | ![Esempio di Prova per Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Aprire [shell.azure.com](https://shell.azure.com) nel browser. | [![Pulsante di avvio di Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Selezionare il pulsante **Cloud Shell** nel menu nell'angolo superiore destro del [portale di Azure](https://portal.azure.com). | ![Pulsante Cloud Shell nel portale di Azure](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Esecuzione di PowerShell in locale**

È anche possibile installare ed eseguire i cmdlet di Azure PowerShell localmente nel computer. I cmdlet di PowerShell vengono aggiornati di frequente. Se non si esegue la versione più recente, i valori specificati nelle istruzioni potrebbero avere esito negativo. Per trovare le versioni di Azure PowerShell installate nel computer, usare il cmdlet `Get-Module -ListAvailable Az`. Per eseguire l'installazione o l'aggiornamento, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Se si esegue PowerShell localmente, assicurarsi di eseguire ' Connect-AzAccount ' per creare la connessione ad Azure.