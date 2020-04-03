---
title: 'Copiare e incollare da e verso una macchina virtuale: Azure BastionCopy and paste to and from a virtual machine: Azure Bastion'
description: In this article, learn how copy and paste to and from an Azure VM using Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619314"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiare e incollare in una macchina virtuale: Azure BastionCopy and paste to a virtual machine: Azure Bastion

Questo articolo consente di copiare e incollare testo da e verso le macchine virtuali quando si usa Azure Bastion.This article helps you copy and paste text to and from virtual machines when using Azure Bastion. Prima di usare una macchina virtuale, assicurarsi di aver seguito la procedura per [creare un host Bastion](bastion-create-host-portal.md). Connettersi quindi alla macchina virtuale che si vuole usare utilizzando [RDP](bastion-connect-vm-rdp.md) o [SSH](bastion-connect-vm-ssh.md).

Per i browser che supportano l'accesso avanzato all'API degli Appunti, è possibile copiare e incollare il testo tra il dispositivo locale e la sessione remota nello stesso modo in cui si copiano e incollano tra le applicazioni nel dispositivo locale. Per altri browser, è possibile utilizzare la tavolozza degli strumenti di accesso agli Appunti del Bastione.

   ![Consenti Appunti](./media/bastion-vm-manage/allow.png)

È supportata solo la copia/incolla del testo. Per le operazioni di copia e incolla dirette, il browser potrebbe richiedere l'accesso agli Appunti durante l'inizializzazione della sessione Bastion. **Consentire** alla pagina Web di accedere agli Appunti.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copiare in una sessione remota

Dopo essersi connessi alla macchina virtuale tramite il portale di [Azure, ](https://portal.azure.com)completare i passaggi seguenti:

1. Copiare il testo/contenuto dal dispositivo locale negli Appunti locali.
1. Durante la sessione remota, avviare la tavolozza degli strumenti di accesso agli Appunti del Bastion selezionando le due frecce. Le frecce si trovano al centro sinistro della sessione.

   ![tavolozza degli strumenti](./media/bastion-vm-manage/left.png)

   ![Appunti](./media/bastion-vm-manage/clipboard.png)
1. In genere, il testo copiato viene visualizzato automaticamente nella tavolozza Incolla copia Bastione. Se il testo non è presente, incollare il testo nell'area di testo della tavolozza.
1. Una volta che il testo si trova nell'area di testo, è possibile incollarlo nella sessione remota.

   ![incollare](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Copia da una sessione remota

Dopo essersi connessi alla macchina virtuale tramite il portale di [Azure, ](https://portal.azure.com)completare i passaggi seguenti:

1. Copiare il testo/contenuto della sessione remota negli Appunti remoti (tramite Ctrl-C).

   ![tavolozza degli strumenti](./media/bastion-vm-manage/remote.png)
1. Durante la sessione remota, avviare la tavolozza degli strumenti di accesso agli Appunti del Bastion selezionando le due frecce. Le frecce si trovano al centro sinistro della sessione.

   ![Appunti](./media/bastion-vm-manage/clipboard2.png)
1. In genere, il testo copiato viene visualizzato automaticamente nella tavolozza Incolla copia Bastione. Se il testo non è presente, incollare il testo nell'area di testo della tavolozza.
1. Una volta che il testo si trova nell'area di testo, è possibile incollarlo nel dispositivo locale.

   ![incollare](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggi le domande frequenti sul [Bastione](bastion-faq.md).