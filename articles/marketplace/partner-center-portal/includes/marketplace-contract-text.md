---
title: File di inclusione
description: Includi il file di testo per Microsoft Commercial Marketplace Standard Contract
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: ChJenk
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: v-chjen
ms.custom: include file
ms.openlocfilehash: 76603ef30cefa14018a7c6b976eb8541b9b608f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76760896"
---
Microsoft fornisce un modello di contratto standard per il Marketplace commerciale.

- **Usare il contratto standard per Microsoft Commercial Marketplace?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard per Microsoft Commercial Marketplace, che consente di semplificare le transazioni nel Marketplace. Anziché creare termini e condizioni personalizzati, gli editori del Marketplace commerciale possono scegliere di offrire il proprio software al di sotto del contratto standard, che i clienti devono solo controllare e accettare una sola volta. Il contratto standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178.

È possibile scegliere di utilizzare il contratto standard anziché fornire i termini e le condizioni personalizzati selezionando la casella di controllo "utilizza il contratto standard per il Marketplace commerciale".

![Uso della casella di controllo contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard **o** a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

**Modifiche contrattuali standard**

Le modifiche contrattuali standard consentono agli editori di selezionare le condizioni di contratto standard per semplificare e personalizzare le condizioni per il prodotto o l'azienda. I clienti devono solo esaminare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori di Marketplace commerciali sono disponibili due tipi di modifiche:

- Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Gli emendamenti universali vengono visualizzati a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa rettifica prima di poter usare l'offerta.
- Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo i clienti del tenant verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!NOTE]
> Questi due tipi di modifiche si impilano tra loro. I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale del contratto standard durante l'acquisto.

**Termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft**: immettere le condizioni per la modifica universale in questa casella. È possibile specificare una singola modifica universale per ogni offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Questi termini vengono visualizzati per i clienti in AppSource, Azure Marketplace e/o portale di Azure durante l'individuazione e il flusso di acquisto.

**Condizioni di modifica personalizzate per il contratto standard per il Marketplace commerciale di Microsoft**: iniziare selezionando **Aggiungi condizioni di modifica personalizzate**. È possibile specificare fino a 10 termini di modifica personalizzati per ogni offerta.

- **Condizioni di modifica personalizzate**: immettere le condizioni di modifica personalizzate nella casella condizioni di modifica personalizzate. In questa casella è possibile immettere un numero illimitato di caratteri. Solo i clienti degli ID tenant specificati per questi termini personalizzati verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una rettifica personalizzata, è necessario specificare almeno un ID tenant. L'ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e trovarlo passando a portal.azure.com > Azure Active Directory > Proprietà. Il valore di ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione del cliente usando l'URL del nome di dominio in cui [si trova il Microsoft Azure e l'ID tenant di Office 365?](https://www.whatismytenantid.com).
- **Descrizione** (facoltativo): specificare facoltativamente una descrizione per l'ID tenant che consente di identificare il cliente di destinazione con la modifica.

**Termini e condizioni**

Se si desidera fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un solo collegamento URL in questo campo in cui è possibile trovare i termini e le condizioni. Verrà visualizzato come collegamento attivo ai clienti.

I clienti devono accettare queste condizioni per poter provare l'offerta.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.
Ricordarsi di **salvare** prima di procedere alla sezione successiva.
