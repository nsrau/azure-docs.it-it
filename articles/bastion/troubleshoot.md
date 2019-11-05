---
title: Risolvere i problemi di Azure Bastion | Microsoft Docs
description: Questo articolo illustra come risolvere i problemi relativi ad Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512951"
---
# <a name="troubleshoot-azure-bastion"></a>Risolvere i problemi di Azure Bastion

Questo articolo illustra come risolvere i problemi relativi ad Azure Bastion.

## <a name="nsg"></a>Non è possibile creare un NSG su AzureBastionSubnet

**D:** Quando si tenta di creare un NSG di sicurezza di rete nella subnet di Azure Bastion, viene ricevuto l'errore seguente: *"il gruppo di sicurezza di rete <NSG name> non ha le regole necessarie per la subnet AzureBastionSubnet di Azure"* .

**R:** Se si crea e si applica un NSG a *AzureBastionSubnet*, assicurarsi di avere aggiunto le regole seguenti nel NSG. Se non si aggiungono queste regole, la creazione o l'aggiornamento di NSG avrà esito negativo.

1. Controllo della connettività del piano: in ingresso su 443 da GatewayManager
2. Registrazione diagnostica e altri utenti: in uscita il 443 a AzureCloud (i tag internazionali all'interno di questo tag di servizio non sono ancora supportati).
3. VM di destinazione: in uscita per 3389 e 22 a VirtualNetwork

Un esempio di regole NSG è disponibile come riferimento nel modello di [avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Per altre informazioni, vedere [NSG Guidance for Azure Bastion](bastion-nsg.md).

## <a name="sshkey"></a>Non è possibile usare la chiave SSH con Azure Bastion

**D:** Quando si tenta di esplorare il file della chiave SSH, viene visualizzato l'errore seguente: *' la chiave privata SSH deve iniziare con-----iniziare-----chiave privata RSA e termina con-----chiave privata rsa-----'* .

**R:** Azure Bastion supporta solo le chiavi SSH RSA, a questo punto nel tempo. Assicurarsi di visualizzare un file di chiave che è una chiave privata RSA per SSH con la chiave pubblica di cui è stato effettuato il provisioning nella macchina virtuale di destinazione. 

Ad esempio, è possibile usare il comando seguente per creare una nuova chiave SSH RSA:

**ssh-keygen-t RSA-b 4096-C "email@domain.com"**

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

## <a name="domain"></a>Non è possibile accedere alla macchina virtuale aggiunta a un dominio Windows

**D:** Non è possibile connettersi alla macchina virtuale Windows aggiunta a un dominio.

**R:** Azure Bastion supporta l'accesso alla macchina virtuale aggiunta a un dominio per l'accesso solo al dominio basato su password utente. Quando si specificano le credenziali del dominio nella portale di Azure, usare il formato UPN (username@domain) invece del formato *DOMINIO\nomeutente* per accedere. Questa operazione è supportata per le macchine virtuali appartenenti a un dominio o ibride (appartenenti a un dominio e unite a Azure AD). Non è supportata per le macchine virtuali con solo join Azure AD.

## <a name="filetransfer"></a>Problemi di trasferimento di file

**D:** Il trasferimento di file è supportato con Azure Bastion?

**R:** Il trasferimento di file non è supportato in questo momento. Stiamo lavorando per aggiungere il supporto.

## <a name="blackscreen"></a>Schermata nera nella portale di Azure

**D:** Quando si tenta di connettersi usando Azure Bastion, viene visualizzata una schermata nera nell'portale di Azure.

**R:** Ciò si verifica quando esiste un problema di connettività di rete tra il Web browser e Azure Bastion (il firewall Internet del client può bloccare il traffico di WebSocket o simile) o tra il Bastion di Azure e la macchina virtuale di destinazione. La maggior parte dei casi include un NSG applicato a AzureBastionSubnet o alla subnet VM di destinazione che blocca il traffico RDP/SSH nella rete virtuale. Consentire il traffico di WebSockets sul firewall Internet client e controllare gruppi nella subnet VM di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md).