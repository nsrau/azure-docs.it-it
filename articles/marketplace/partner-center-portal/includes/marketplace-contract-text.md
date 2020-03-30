---
title: File di inclusione
description: includere il file di testo per il contratto standard del mercato commerciale Microsoft
documentationcenter: partner-center-commercial-marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: 9b99876b66826cc5a66cba686a7d6a5243b47b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277223"
---
Microsoft fornisce un modello di contratto standard per il marketplace commerciale.

- **Utilizzare il contratto standard per il marketplace commerciale Microsoft?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legale per i fornitori di software, Microsoft offre un contratto standard per il marketplace commerciale Microsoft per facilitare le transazioni nel marketplace. Invece di creare termini e condizioni personalizzati, gli editori del mercato commerciale possono scegliere di offrire il proprio software ai sensi del Contratto Standard, che i clienti devono solo controllare e accettare una volta. Il Contratto Standard può https://go.microsoft.com/fwlink/?linkid=2041178essere trovato qui: .

Puoi scegliere di utilizzare il Contratto Standard invece di fornire termini e condizioni personalizzati selezionando la casella di controllo "Usa il contratto standard per il mercato commerciale".

![Utilizzo della casella di controllo Contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo aver pubblicato un'offerta utilizzando il contratto Standard per il mercato commerciale Microsoft, non è possibile utilizzare i termini e le condizioni personalizzati. È uno scenario "o". Offrite la vostra soluzione in base al Contratto Standard **o** ai vostri termini e condizioni. Se si desidera modificare i termini del Contratto Standard, è possibile farlo tramite Le modifiche del contratto standard.

**Modifiche del contratto standard**

Le modifiche del contratto standard consentono agli editori di selezionare i termini del contratto standard per semplicità e personalizzare i termini per il loro prodotto o azienda. I clienti devono rivedere le modifiche al contratto solo se hanno già esaminato e accettato il contratto standard Microsoft.

Sono disponibili due tipi di emendamenti per gli editori del mercato commerciale:

- Emendamenti universali: questi emendamenti sono applicati universalmente al contratto standard per tutti i clienti. Gli emendamenti universali vengono mostrati a tutti i clienti dell'offerta nel flusso di acquisto. I clienti devono accettare i termini del Contratto Standard e la modifica prima di poter utilizzare l'offerta.
- Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a clienti specifici solo tramite ID tenant di Azure.Custom Amendments: These amendments are special amendments to the Standard Contract that are targeted to specific customers only via Azure tenant IDs. I publisher possono scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentati i termini di modifica personalizzati nel flusso di acquisto dell'offerta.  I clienti devono accettare i termini del Contratto Standard e gli emendamenti prima di poter utilizzare l'offerta.

>[!NOTE]
> Questi due tipi di emendamenti si sovrappongono l'uno sull'altro. I clienti mirati con modifiche personalizzate otterranno anche la modifica universale al contratto standard durante l'acquisto.

Termini di **modifica universale per il contratto standard per il mercato commerciale di Microsoft**: Immettere i termini di modifica universale in questa casella. È possibile fornire un unico emendamento universale per ogni offerta. È possibile immettere un numero illimitato di caratteri in questa casella. Questi termini vengono visualizzati ai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

Termini di **modifica personalizzati per il contratto standard per il mercato commerciale di Microsoft**: Iniziare selezionando Aggiungi termini di modifica **personalizzata**. Puoi fornire fino a 10 termini di modifica personalizzati per offerta.

- **Termini di modifica personalizzata**: immettere i termini della modifica personalizzata nella casella Termini di modifica personalizzati. È possibile immettere un numero illimitato di caratteri in questa casella. Solo i clienti degli ID tenant specificati per questi termini personalizzati verranno presentati con i termini di modifica personalizzati nel flusso di acquisto dell'offerta nel portale di Azure.Only customers from the tenant IDs you specify for these custom terms will be presented the custom amendment terms in the offer's purchase flow in the Azure portal.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario fornire almeno un ID tenant. L'ID tenant identifica il cliente in Azure.The tenant ID identifies your customer in Azure. È possibile chiedere questo ID al cliente e può trovarlo passando a portal.azure.com > Azure Active Directory > Properties.You can ask your customer for this ID and they can find it by navigating to portal.azure.com > Azure Active Directory > Properties. Il valore dell'ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare l'ID tenant dell'organizzazione del cliente utilizzando l'URL del nome di dominio in [Che cos'è l'ID tenant di Microsoft Azure e Office 365?](https://www.whatismytenantid.com).
- **Descrizione** (facoltativo): fornisci facoltativamente una descrizione descrittiva dell'ID tenant che ti aiuti a identificare il cliente a cui ti rivolgi con la modifica.

**Termini e condizioni**

Se si desidera fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo Termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, inserisci un singolo collegamento URL in questo campo in cui è possibile trovare i termini e le condizioni. Verrà visualizzato ai clienti come collegamento attivo.

I clienti sono tenuti ad accettare questi termini prima di poter provare la tua offerta.

Ricordarsi di **salvare** prima di passare alla sezione successiva.
Ricordarsi di **salvare** prima di passare alla sezione successiva.
