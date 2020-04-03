---
title: Risolvere i problemi relativi a I piedi di Azure Bastion Documenti Microsoft
description: In this article, learn how to troubleshoot Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619175"
---
# <a name="troubleshoot-azure-bastion"></a>Risolvere i problemi di Azure Bastion

Questo articolo illustra come risolvere i problemi di Azure Bastion.This article shows you how to troubleshoot Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Impossibile creare un gruppo di sicurezza di rete in AzureBastionSubnetUnable to create an NSG on AzureBastionSubnet

**D:** Quando si tenta di creare un gruppo di sicurezza di rete nella subnet di Azure Bastion, viene visualizzato l'errore seguente: 'Gruppo di sicurezza di *rete <NSG name> non dispone di regole necessarie per Azure Bastion Subnet AzureBastionSubnet"*.

**A:** Se si crea e si applica un gruppo di sicurezza di rete ad *AzureBastionSubnet*, assicurarsi di aver aggiunto le regole seguenti nel gruppo di sicurezza di rete. Se non si aggiungono queste regole, la creazione/aggiornamento del gruppo di sicurezza di rete avrà esito negativo.

1. Connettività del piano di controllo : in ingresso su 443 da GatewayManagerControl plane connectivity – Inbound on 443 from GatewayManager
2. Registrazione diagnostica e altri: in uscita in uscita dal 443 in AzureCloud (i tag regionali all'interno di questo tag di servizio non sono ancora supportati).
3. Macchina virtuale di destinazione: in uscita per 3389 e 22 in VirtualNetworkTarget VM – Outbound for 3389 and 22 to VirtualNetwork

Un esempio delle regole del gruppo di sicurezza di gruppo è disponibile come riferimento nel [modello di avvio rapido.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)
Per altre informazioni, vedere Indicazioni del gruppo di sicurezza di rete per Azure Bastion .For more information, see [NSG guidance for Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Impossibile utilizzare la chiave SSH con Azure Bastion

**D:** Quando si tenta di sfogliare il file di chiave SSH, viene visualizzato il seguente errore: *'SSH La chiave privata deve iniziare con -----BEGIN RSA PRIVATE KEY----- e termina con -----END RSA PRIVATE KEY-----'.*

**A:** Il Bastione di Azure supporta solo chiavi RSA SSH, a questo punto nel tempo. Assicurarsi di esplorare un file di chiave che è una chiave privata RSA per SSH, con la chiave pubblica di cui è stato eseguito il provisioning nella macchina virtuale di destinazione. 

Ad esempio, è possibile utilizzare il comando seguente per creare una nuova chiave RSA SSH:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Impossibile accedere alla macchina virtuale aggiunta a un dominio Windows

**D:** Non riesco a connettermi alla mia macchina virtuale Windows che fa parte di un dominio.

**A:** Azure Bastion supporta l'accesso alla macchina virtuale aggiunta al dominio solo per l'accesso al dominio basato su password utente. Quando si specificano le credenziali di dominio nelusername@domainportale di Azure, usare il formato UPN ( ) anziché il formato *dominio/nome utente* per accedere. Questa funzionalità è supportata per le macchine virtuali aggiunte a un dominio o a ibridi (sia appartenenti a un dominio che ad Azure AD). Non è supportato per le macchine virtuali solo aggiunte ad Azure AD.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemi di trasferimento dei file

**D:** Il trasferimento di file è supportato con Azure Bastion?

**A:** Il trasferimento di file non è attualmente supportato. Stiamo lavorando per aggiungere supporto.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Schermata nera nel portale di Azure

**D:** Quando si tenta di connettersi usando Azure Bastion, viene visualizzata una schermata nera nel portale di Azure.When I try to connect using Azure Bastion, I get a black screen in the Azure portal.

**A:** Ciò si verifica quando si verifica un problema di connettività di rete tra il Web browser e Azure Bastion (il firewall Internet client potrebbe bloccare il traffico WebSockets o simile) o tra il Bastione di Azure e la macchina virtuale di destinazione. La maggior parte dei casi include un gruppo di sicurezza di rete applicato ad AzureBastionSubnet o nella subnet della macchina virtuale di destinazione che blocca il traffico RDP/SSH nella rete virtuale. Consentire il traffico WebSockets sul firewall Internet client e controllare i gruppi di sicurezza di rete nella subnet della macchina virtuale di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md).