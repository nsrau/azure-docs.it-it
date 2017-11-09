---
title: Aggiungere una macchina virtuale Windows Server ad Azure Active Directory Domain Services | Microsoft Docs
description: Aggiungere una macchina virtuale Windows Server a un dominio gestito usando i modelli di Azure Resource Manager.
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 8cfa13ee028b5e367158de42ceab0a1cd99d45c2
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito usando un modello di Resource Manager
Questo articolo illustra come aggiungere una macchina virtuale Windows Server a un dominio gestito Azure AD Domain Services, usando i modelli Resource Manager.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:
1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Assicurarsi di aver configurato gli indirizzi IP del dominio gestito come server DNS per la rete virtuale. Per altre informazioni, vedere la [procedura per aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-dns.md).
5. Completare i passaggi necessari per [sincronizzare le password nel dominio gestito di Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Installare e configurare gli strumenti richiesti
Per eseguire i passaggi descritti in questo documento, è possibile usare una delle opzioni seguenti:
* **Azure PowerShell**: [Installare e configurare](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Interfaccia della riga di comando multipiattaforma di Azure**: [Installare e configurare](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opzione 1: Eseguire il provisioning di una nuova macchina virtuale Windows Server e aggiungerla a un dominio gestito
**Nome del modello di avvio rapido**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Per distribuire una macchina virtuale Windows Server e aggiungerla a un dominio gestito, seguire questa procedura:
1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Fare clic su **Distribuzione in Azure**.
3. Nella pagina **Distribuzione personalizzata** fornire le informazioni necessarie per eseguire il provisioning della macchina virtuale.
4. Selezionare la **sottoscrizione di Azure** in cui eseguire il provisioning della macchina virtuale. Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services.
5. Scegliere un **gruppo di risorse** esistente oppure crearne uno nuovo.
6. Selezionare un **percorso** in cui distribuire la nuova macchina virtuale.
7. Nella pagina **Existing VNET Name** (Nome rete virtuale esistente) specificare la rete virtuale in cui viene distribuito il dominio gestito di Azure AD Domain Services.
8. In **Existing Subnet Name** (Nome subnet esistente) specificare la subnet nella rete virtuale in cui distribuire questa macchina virtuale. Non selezionare la subnet del gateway nella rete virtuale. Inoltre non selezionare la subnet dedicata in cui viene distribuito il dominio gestito.
9. In **DNS Label Prefix** (Prefisso etichetta DNS) specificare il nome host per la macchina virtuale in corso di provisioning. Un esempio è "contoso-win".
10. Selezionare il valore **Dimensioni macchina virtuale** appropriato per la macchina virtuale.
11. In **Domain To Join** (Dominio da aggiungere) specificare il nome di dominio DNS del dominio gestito.
12. In **Nome utente di dominio** specificare il nome dell'account utente nel dominio gestito che deve essere usato per aggiungere la macchina virtuale al dominio gestito.
13. In **Password del dominio** specificare la password dell'account utente di dominio referenziato dal parametro 'domainUsername'.
14. Facoltativo: è possibile specificare un **percorso OU** a un'unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta alla OU **AAD DC Computers** predefinita nel dominio gestito.
15. Nel campo **VM Admin Username** (Nome utente amministratore VM) specificare il nome di un account di amministratore locale per la macchina virtuale.
16. Nel campo **VM Admin Password** (Password amministratore VM) specificare una password di amministratore locale per la macchina virtuale. Specificare una password di amministratore locale sicura per la macchina virtuale per assicurare la protezione da attacchi di forza bruta alle password.
17. Fare clic su **Accetto le condizioni riportate sopra**.
18. Fare clic su **Acquista** per effettuare il provisioning della macchina virtuale.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri del modello contiene le password degli account di dominio e le password di amministratore locale per la macchina virtuale. Assicurarsi di non lasciare questo file in condivisioni file o altri percorsi condivisi. Si consiglia di eliminare questo file al termine della distribuzione delle macchine virtuali.
>

Dopo avere completato la distribuzione, al dominio gestito viene aggiunta la macchina virtuale Windows di cui è stato appena eseguito il provisioning.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opzione 2: aggiungere una VM Windows Server esistente a un dominio gestito
**Modello di avvio rapido**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Per aggiungere una macchina virtuale Windows Server esistente a un dominio gestito, seguire questa procedura:
1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Fare clic su **Distribuzione in Azure**.
3. Nella pagina **Distribuzione personalizzata** fornire le informazioni necessarie per eseguire il provisioning della macchina virtuale.
4. Selezionare la **sottoscrizione di Azure** in cui eseguire il provisioning della macchina virtuale. Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services.
5. Scegliere un **gruppo di risorse** esistente oppure crearne uno nuovo.
6. Selezionare un **percorso** in cui distribuire la nuova macchina virtuale.
7. Nel campo di **elenco delle macchine virtuali** specificare i nomi delle macchine virtuali esistenti da aggiungere al dominio gestito. Usare una virgola per separare i nomi delle singole macchine virtuali. Ad esempio, **contoso-web, contoso-api**.
8. In **Domain Join User Name** (Nome utente aggiunta al dominio) specificare il nome dell'account utente nel dominio gestito che deve essere usato per aggiungere la macchina virtuale al dominio gestito.
9. In **Domain Join User Password** (Password utente aggiunta al dominio) specificare la password dell'account utente di dominio referenziato dal parametro 'domainUsername'.
10. In **FQDN dominio** specificare il nome di dominio DNS del dominio gestito.
11. Facoltativo: è possibile specificare un **percorso OU** a un'unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta alla OU **AAD DC Computers** predefinita nel dominio gestito.
12. Fare clic su **Accetto le condizioni riportate sopra**.
13. Fare clic su **Acquista** per effettuare il provisioning della macchina virtuale.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri del modello contiene le password degli account di dominio e le password di amministratore locale per la macchina virtuale. Assicurarsi di non lasciare questo file in condivisioni file o altri percorsi condivisi. Si consiglia di eliminare questo file al termine della distribuzione delle macchine virtuali.
>

Al termine della distribuzione le macchine virtuali Windows specificate vengono aggiunte al dominio gestito.


## <a name="related-content"></a>Contenuti correlati
* [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Modello di avvio rapido di Azure - Aggiunta al dominio di una nuova macchina virtuale](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modello di avvio rapido di Azure - Aggiunta al dominio di macchine virtuali esistenti](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
