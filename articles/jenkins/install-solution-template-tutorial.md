---
title: Esercitazione - Creare un server Jenkins in Azure
description: Questa esercitazione illustra come istallare Jenkins in una macchina virtuale Linux di Azure dal modello di soluzione Jenkins e compilare un'applicazione Java di esempio.
keywords: jenkins, azure, devops, portale, macchina virtuale, modello di soluzione
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274530"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Esercitazione: Creare un server Jenkins in una macchina virtuale Linux di Azure 

Questa esercitazione illustra come installare [Jenkins](https://jenkins.io) in una macchina virtuale Ubuntu Linux con gli strumenti e i plug-in configurati per usare Azure. Al termine, si avrà un server Jenkins in esecuzione in Azure che compila un'app Java di esempio da [GitHub](https://github.com).

> [!div class="checklist"]
> * Installare e configurare un server Jenkins in Azure
> * Accedere alla console di Jenkins usando un tunnel SSH
> * Creare un progetto Freestyle
> * Compilare il codice e creare il pacchetto dell'app di esempio

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]