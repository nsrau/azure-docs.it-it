---
title: Soluzione VMware di Azure di CloudSimple-configurare tabelle e regole del firewall
description: Viene descritto come configurare le regole e le tabelle del firewall del cloud privato per limitare il traffico su subnet e VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 411b4bb74c21a445f4001c949e1c7811af212453
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606445"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurare le regole e le tabelle del firewall per i cloud privati

Le tabelle del firewall e le regole associate consentono di specificare restrizioni sul traffico da applicare a specifiche subnet e VLAN.

* Una subnet può essere associata a una tabella del firewall.
* Una tabella del firewall può essere associata a più subnet.

## <a name="add-a-new-firewall-table"></a>Aggiungere una nuova tabella firewall

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md) e selezionare **rete** dal menu laterale.
2. Selezionare le **tabelle del firewall**.
3. Selezionare **Crea tabella firewall**.

    ![Pagina VLAN/subnet](media/firewall-tables-page.png)

4. Immettere un nome per la tabella.
5. Viene elencata una regola predefinita per la tabella. Fare clic su **Crea nuova regola** per creare una regola aggiuntiva. Per informazioni dettagliate, vedere la procedura riportata di seguito.
6. Fare clic su **fine** per salvare la tabella del firewall.

> [!IMPORTANT]
> È possibile creare fino a due tabelle del firewall per ogni cloud privato.

## <a name="firewall-rules"></a>Regole del firewall

Le regole del firewall determinano il modo in cui il firewall tratta tipi specifici di traffico. Nella scheda **regole** per una tabella del firewall selezionata sono elencate tutte le regole associate.

![Tabella delle regole del firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Creare una regola del firewall

1. Visualizzare le impostazioni per creare una regola del firewall in uno dei modi seguenti:
    * Fare clic su **Aggiungi regola** quando si crea una tabella del firewall.
    * Selezionare una particolare tabella del firewall nella pagina **rete > firewall tabelle** e fare clic su **Crea nuova regola firewall**.
2. Configurare la regola come indicato di seguito:
    * **Nome**. Assegnare un nome alla regola.
    * **Priorità**. Assegnare una priorità alla regola. Vengono eseguite per prime le regole con numeri più bassi.
    * **Tipo di traffico**. Consente di indicare se la regola è destinata a un cloud privato, a Internet o a un traffico VPN (senza stato) o per un indirizzo IP pubblico (con stato).
    * **Protocollo**. Selezionare il protocollo coperto dalla regola (TCP, UDP o qualsiasi protocollo).
    * **Direzione**. Consente di indicare se la regola è per il traffico in ingresso o in uscita. È necessario definire regole separate per il traffico in ingresso e in uscita.
    * **Azione**. Selezionare l'azione da eseguire se la regola corrisponde a (Consenti o nega).
    * **Source**. Specificare le origini coperte dalla regola (blocco CIDR, interno o qualsiasi origine).
    * **Intervallo di porte di origine**. Specificare l'intervallo di porte soggetto alla regola.
    * **Direzione**. Selezionare in ingresso o in uscita.
    * **Destinazione**. Specificare le destinazioni coperte dalla regola (blocco CIDR, interno o qualsiasi origine).
    * **Intervallo di porte di origine**. Specificare l'intervallo di porte soggetto alla regola.

    ![Regola di aggiunta della tabella del firewall](media/firewall-rule-create.png)

3. Fare clic su **fine** per salvare la regola e aggiungerla all'elenco di regole per la tabella del firewall.

> [!IMPORTANT]
> Ogni tabella del firewall può avere fino a 10 regole in ingresso e 20 regole in uscita. Questi limiti possono essere aumentati [contattando il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a>Connetti VLAN/subnet

Dopo aver definito una tabella del firewall, è possibile specificare le subnet soggette alle regole della tabella.

1. Nella pagina **Network** > **Firewall Tables** Selezionare una tabella del firewall.
2. Aprire la scheda **VLAN/subnet collegata** .
3. Fare clic su **Connetti a una VLAN/subnet**.
4. Selezionare il cloud privato e la VLAN. Vengono visualizzati il nome della subnet e il blocco CIDR associati.
5. Fare clic su **Submit**.
