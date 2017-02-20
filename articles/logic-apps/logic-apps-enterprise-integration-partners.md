---
title: Informazioni sui partner ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 2549531d21c8e15e5bbb4321c4119e6aaac53e96
ms.openlocfilehash: de12b83c811dcdd93ed691ddade9d748383110df


---
# <a name="partners-in-b2b-scenarios"></a>Partner di scenari B2B

I partner sono le entità che partecipano alle transazioni e allo scambio di messaggi Business-To-Business (B2B). Prima di poter creare i partner che rappresentano l'utente e l'altra organizzazione nelle transazioni, è necessario che entrambe le parti condividano le informazioni che identificano e convalidano i messaggi inviati. Dopo aver definito questi dettagli e quando si è pronti per avviare una relazione commerciale, è possibile creare i partner nell'account di integrazione che rappresentino entrambe le parti.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>Che ruoli hanno i partner nell'account di integrazione?

Per definire i dettagli dello scambio di messaggi tra i partner, è necessario creare contratti tra i partner. Tuttavia, prima di creare un contratto è necessario aggiungere almeno due partner all'account di integrazione. L'organizzazione deve far parte del contratto in qualità di **partner host**. L'altro partner, o **partner guest**, rappresenta l'organizzazione che scambia i messaggi con l'organizzazione. Il partner guest può essere un'altra società o anche un reparto all'interno della stessa organizzazione.

Dopo aver aggiunto i partner, è possibile creare un contratto.

Le impostazioni per la ricezione e l'invio sono configurate dal punto di vista del partner host. Le impostazioni di ricezione del contratto, ad esempio, determinano la modalità con cui il partner host riceve i messaggi inviati da un partner guest. Analogamente, le impostazioni di invio del contratto indicano in che modo il partner host invia messaggi al partner guest.

## <a name="how-to-create-a-partner"></a>Procedura: Creare un partner

1. Nel Portale di Azure selezionare **Sfoglia**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Nella casella di ricerca per filtro immettere **integrazione**, quindi selezionare **Account di integrazione** dall'elenco dei risultati.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione a cui si desidera aggiungere i partner.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selezionare il riquadro **Partner** .

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Nel pannello dei partner, scegliere **Aggiungi**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Immettere il nome del partner, quindi selezionare un **Qualificatore**. Infine, inserire un **valore** per identificare i documenti ricevuti dalle app.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Selezionare l'icona di notifica a forma di *campana* per visualizzare lo stato di avanzamento del processo di creazione del partner.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Per verificare che i nuovi partner sono stati aggiunti correttamente, selezionare il riquadro **Partner**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Dopo aver selezionato il riquadro Partner, anche i partner appena aggiunti vengono visualizzati nel pannello Partner.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Come modificare i partner esistenti nell'account di integrazione

1. Selezionare il riquadro **Partner** .
2. Selezionare il partner che si desidera modificare quando si apre il pannello Partner.
3. Nel riquadro **Aggiorna partner** apportare le modifiche necessarie.
4. Al termine, scegliere **Salva** o **Ignora** per annullare le modifiche.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Procedura: Eliminare un partner

1. Selezionare il riquadro **Partner** .
2. Selezionare il partner che si desidera eliminare quando si apre il pannello Partner.
3. Scegliere **Elimina**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  




<!--HONumber=Feb17_HO2-->


