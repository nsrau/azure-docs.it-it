---
title: Risolvere i problemi di Azure Bastion | Microsoft Docs
description: Questo articolo illustra come risolvere i problemi relativi ad Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: f3c142491363f30513877ae4368f291430aa3675
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831931"
---
# <a name="troubleshoot-azure-bastion"></a>Risolvere i problemi di Azure Bastion

Questo articolo illustra come risolvere i problemi relativi ad Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Non è possibile creare un NSG su AzureBastionSubnet

**D:** Quando si tenta di creare un NSG di sicurezza di rete nella subnet di Azure Bastion, viene ricevuto l'errore seguente: *"il gruppo di sicurezza di rete non <NSG name> ha le regole necessarie per la subnet di Azure Bastion AzureBastionSubnet"*.

**R:** Se si crea e si applica un NSG a *AzureBastionSubnet*, assicurarsi di avere aggiunto le regole seguenti nel NSG. Se non si aggiungono queste regole, la creazione o l'aggiornamento di NSG avrà esito negativo.

1. Controllo della connettività del piano: in ingresso su 443 da GatewayManager
2. Registrazione diagnostica e altri utenti: in uscita il 443 a AzureCloud (i tag internazionali all'interno di questo tag di servizio non sono ancora supportati).
3. VM di destinazione: in uscita per 3389 e 22 a VirtualNetwork

Un esempio di regole NSG è disponibile come riferimento nel modello di [avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).
Per altre informazioni, vedere [NSG Guidance for Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Non è possibile usare la chiave SSH con Azure Bastion

**D:** Quando si tenta di esplorare il file della chiave SSH, viene visualizzato l'errore seguente: *' la chiave privata SSH deve iniziare con-----iniziare-----chiave privata RSA e termina con-----chiave privata rsa-----'*.

**R:** Azure Bastion supporta solo le chiavi SSH RSA, a questo punto nel tempo. Assicurarsi di visualizzare un file di chiave che è una chiave privata RSA per SSH con la chiave pubblica di cui è stato effettuato il provisioning nella macchina virtuale di destinazione. 

Ad esempio, è possibile usare il comando seguente per creare una nuova chiave SSH RSA:

**ssh-keygen-t RSA-b 4096-C " email@domain.com "**

Output:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Non è possibile accedere alla macchina virtuale aggiunta a un dominio Windows

**D:** Non è possibile connettersi alla macchina virtuale Windows aggiunta a un dominio.

**R:** Azure Bastion supporta l'accesso alla macchina virtuale aggiunta a un dominio per l'accesso solo al dominio basato su password utente. Quando si specificano le credenziali del dominio nella portale di Azure, usare il username@domain formato UPN () invece del formato *DOMINIO\nomeutente* per accedere. Questa operazione è supportata per le macchine virtuali appartenenti a un dominio o ibride (appartenenti a un dominio e unite a Azure AD). Non è supportata per le macchine virtuali con solo join Azure AD.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemi di trasferimento di file

**D:** Il trasferimento di file è supportato con Azure Bastion?

**R:** Il trasferimento di file non è supportato in questo momento. Stiamo lavorando per aggiungere il supporto.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Schermata nera nella portale di Azure

**D:** Quando si tenta di connettersi usando Azure Bastion, viene visualizzata una schermata nera nell'portale di Azure.

**R:** Ciò si verifica quando esiste un problema di connettività di rete tra il Web browser e Azure Bastion (il firewall Internet del client può bloccare il traffico di WebSocket o simile) o tra il Bastion di Azure e la macchina virtuale di destinazione. La maggior parte dei casi include un NSG applicato a AzureBastionSubnet o alla subnet VM di destinazione che blocca il traffico RDP/SSH nella rete virtuale. Consentire il traffico di WebSockets sul firewall Internet client e controllare gruppi nella subnet VM di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md).
