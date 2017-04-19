---
title: Macchine virtuali Linux di Azure - Esercitazione .NET Core 1 | Documentazione Microsoft
description: Macchine virtuali di Azure - Esercitazione DotNet Core
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b3652e86-0c44-4ac9-8cd1-27abdeaea4d4
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b5f6a535068f7a2cac4bac607dc56e356a97567e
ms.lasthandoff: 04/03/2017


---
# <a name="automating-application-deployments-to-linux-virtual-machines"></a>Automazione della distribuzione di applicazioni nelle macchine virtuali Linux 

Questa serie di quattro articoli descrive in dettaglio la distribuzione e la configurazione di risorse e applicazioni di Azure mediante modelli di Azure Resource Manager. In questa serie viene distribuito un modello di esempio e viene esaminato il modello di distribuzione. L'obiettivo di questa serie è illustrare la relazione tra le risorse di Azure e fornire suggerimenti pratici per la distribuzione di modelli di Azure Resource Manager completamente integrati. Questo documento presuppone un livello base di conoscenze relative ad Azure Resource Manager. Prima di iniziare questa esercitazione, acquisire familiarità con i concetti fondamentali di Azure Resource Manager. 

## <a name="music-store-application"></a>Applicazione Music Store
L'esempio usato in questa serie consiste in un'applicazione .NET Core che simula un'esperienza di acquisto su Music Store. L'applicazione può essere distribuita in un sistema virtuale Windows o Linux. Sono state create distribuzioni di esempio per entrambi i sistemi. L'applicazione include un'applicazione Web e un database SQL. Prima di leggere gli articoli di questa serie, distribuire l'applicazione usando il pulsante di distribuzione presente in questa pagina. Al termine della distribuzione, l'applicazione e l'architettura di Azure avranno una struttura simile al diagramma seguente. 

Per il modello Music Store di Resource Manager, accedere alla pagina [Music Store Linux Template](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db) (Modello Music Store per Linux)

![Applicazione Music Store](./media/dotnet-core-1-landing/music-store.png)

Ognuno di questi componenti, incluso il codice JSON associato al modello, viene esaminato nei quattro articoli seguenti.

* [**Architettura dell'applicazione**](dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): i componenti di un'applicazione, ad esempio i siti Web e i database, devono essere ospitati nelle risorse del computer di Azure, vale a dire in macchine virtuali e database SQL di Azure. Questo documento illustra come mappare i requisiti di calcolo alle risorse di Azure e come distribuire queste risorse con un modello di Azure Resource Manager. 
* [**Accesso e sicurezza**](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): quando si esegue l'hosting di applicazioni in Azure, è necessario considerare in che modo si accede all'applicazione e come i vari componenti dell'applicazione eseguono l'accesso. Questo documento descrive come fornire e proteggere l'accesso Internet per un'applicazione e l'accesso tra i relativi componenti.
* [**Disponibilità e scalabilità**](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): queste due caratteristiche fanno riferimento alla capacità di un'applicazione di rimanere in esecuzione durante il tempo di inattività dell'infrastruttura e di ridimensionare le risorse di calcolo in base alla domanda. Questo documento descrive i componenti necessari per distribuire un'applicazione con carico bilanciato e disponibilità elevata.
* [**Distribuzione dell'applicazione**](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): quando si distribuiscono le applicazioni nelle macchine virtuali di Azure, è necessario considerare il metodo da usare per installare i file binari dell'applicazione nella macchina virtuale. Questo documento descrive come automatizzare l'installazione di applicazioni mediante le estensioni script personalizzate per le macchine virtuali di Azure.

Quando si sviluppano modelli di Azure Resource Manager, l'obiettivo è quello di automatizzare la distribuzione dell'infrastruttura di Azure, nonché l'installazione e la configurazione delle applicazioni ospitate in questa infrastruttura. Le informazioni riportate in questi articoli offrono un esempio di questa esperienza.

## <a name="deploy-the-music-store-application"></a>Distribuire l'applicazione Music Store
Per distribuire l'applicazione Music Store è possibile usare questo pulsante.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Per il modello di Azure Resource Manager è necessario impostare i valori dei parametri seguenti.

| Nome parametro | Descrizione |
| --- | --- |
| SSHKEYDATA |Dati della chiave SSH usati per proteggere l'accesso alla macchina virtuale. Per informazioni sulla creazione di una coppia di chiavi SSH, vedere [Creare chiavi SSH in Linux e Mac per le VM Linux in Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). |
| ADMINUSERNAME |Nome utente dell'amministratore usato per la macchina virtuale e per il database SQL di Azure. |
| SQLADMINPASSWORD |Password usata per il database SQL di Azure. |
| NUMBEROFINSTANCES |Numero di macchine virtuali da creare. Ognuna di queste macchine ospita l'applicazione Web Music Store e tutto il traffico tra le macchine virtuali è sottoposto a bilanciamento del carico. |
| PUBLICIPADDRESSDNSNAME |Nome DNS univoco globale associato all'indirizzo IP pubblico. |

Al termine della distribuzione del modello, passare all'indirizzo IP pubblico usando un browser Internet. Verrà visualizzato il sito Music Store .NET Core.

## <a name="next-steps"></a>Passaggi successivi
<hr>

[Passaggio 1: Architettura delle applicazioni con i modelli di Azure Resource Manager](dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Passaggio 2: Accesso e sicurezza nei modelli di Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Passaggio 3: Disponibilità e scalabilità nei modelli di Azure Resource Manager](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Passaggio 4: Distribuzione di applicazioni con i modelli di Azure Resource Manager](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


