---
title: File di inclusione
description: Includi il file di testo per Microsoft Commercial Marketplace Standard Contract
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d899db8b5f6f3b7c0ccef4e44c814f1e126c626a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326673"
---
Microsoft fornisce un modello di contratto standard per il Marketplace commerciale.

- **Usare il contratto standard per il Marketplace commerciale di Microsoft?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un modello di contratto standard per facilitare le transazioni nel marketplace. Anziché creare termini e condizioni personalizzati, gli editori del marketplace commerciale possono scegliere di offrire il proprio software ai sensi del contratto standard, che i clienti devono esaminare e accettare una sola volta. Il contratto standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178 .

È possibile scegliere di usare il contratto standard anziché fornire termini e condizioni personalizzati selezionando la casella di controllo "Usare il contratto standard per il Marketplace commerciale di Microsoft?".

![Casella di controllo per l'uso del contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il marketplace commerciale Microsoft, non è possibile usare termini e condizioni personalizzati. La scelta di uno scenario esclude automaticamente l'altro. È possibile offrire la propria soluzione ai sensi del contratto standard **o** di termini e condizioni personalizzati. Per modificare le condizioni del contratto standard, è possibile usare le modifiche contrattuali standard.

**Modifiche contrattuali standard**

Le modifiche contrattuali standard consentono agli editori di selezionare le condizioni del contratto standard per semplificare e personalizzare le condizioni per il prodotto o l'azienda. I clienti devono esaminare solo le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori del marketplace commerciale sono disponibili due tipi di modifiche:

- Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Le modifiche universali vengono visualizzate a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa modifica prima di poter usare l'offerta.
- Modifiche personalizzate: si tratta di modifiche speciali apportate al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Le condizioni di modifica personalizzate verranno presentate solo ai clienti del tenant nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!NOTE]
> Questi due tipi di modifiche si integrano tra loro. I clienti cui sono destinate le modifiche personalizzate riceveranno anche la modifica universale al contratto standard durante l'acquisto.

**Termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft**: immettere le condizioni per la modifica universale in questa casella. È possibile apportare una singola modifica universale per offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

**Condizioni di modifica personalizzate per il contratto standard per il Marketplace commerciale di Microsoft**: iniziare selezionando **Aggiungi condizioni di modifica personalizzate**. È possibile specificare fino a 10 condizioni di modifica personalizzate per ogni offerta.

- **Condizioni di modifica personalizzate**: immettere le condizioni di modifica personalizzate nella casella condizioni di modifica personalizzate. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni verranno visualizzate solo ai clienti degli ID tenant specificati per queste condizioni personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario specificare almeno un ID tenant. L'ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e il cliente può trovarlo passando a portal.azure.com > Azure Active Directory > Proprietà. Il valore dell'ID directory è l'ID tenant, ad esempio 50c464d3-4930-494c-963C-1e951d15360e. È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com).
- **Descrizione** (facoltativo): specificare facoltativamente una descrizione per l'ID tenant che consente di identificare il cliente di destinazione con la modifica.

**Termini e condizioni**

Se si vuole fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo Termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un unico collegamento URL in questo campo tramite il quale è possibile accedere ai termini e condizioni. Verrà visualizzato ai clienti come collegamento attivo.

I clienti sono tenuti ad accettare le condizioni prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.
