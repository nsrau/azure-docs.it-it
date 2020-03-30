---
title: Azure VMware Solution by CloudSimple - Configurare tabelle e regole del firewall
description: Descrive come configurare le tabelle e le regole del firewall del cloud privato per limitare il traffico su subnet e VLAN.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244667"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurare tabelle e regole del firewall per i cloud privati

Le tabelle del firewall e le regole associate consentono di specificare restrizioni sul traffico da applicare a subnet e VLAN specifiche.

* Una subnet può essere associata a una tabella del firewall.
* Una tabella firewall può essere associata a più subnet.

## <a name="add-a-new-firewall-table"></a>Aggiungere una nuova tabella firewall

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md) e selezionare **Rete** nel menu laterale.
2. Selezionare **Tabelle firewall**.
3. Selezionare **Crea tabella firewall**.

    ![VLAN/pagina subnet](media/firewall-tables-page.png)

4. Immettere un nome per la tabella.
5. Viene elencata una regola predefinita per la tabella. Fare clic su **Crea nuova regola** per creare una regola aggiuntiva. Per informazioni dettagliate, vedere la procedura seguente.
6. Fare clic su **Fine** per salvare la tabella del firewall.

> [!IMPORTANT]
> È possibile creare fino a due tabelle Firewall per il cloud privato.

## <a name="firewall-rules"></a>Regole del firewall

Le regole del firewall determinano il modo in cui il firewall tratta tipi specifici di traffico. Nella scheda **Regole** per una tabella firewall selezionata sono elencate tutte le regole associate.

![Tabella delle regole del firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Creare una regola del firewall

1. Visualizzare le impostazioni per creare una regola firewall in uno dei seguenti modi:
    * Fare clic su **Aggiungi regola** durante la creazione di una tabella firewall.
    * Selezionare una particolare tabella firewall nella pagina **Tabelle > firewall** di rete e fare clic su Crea nuova regola **firewall**.
2. Impostare la regola come segue:
    * **Nome**. Assegnare un nome alla regola.
    * **Priorità**. Assegnare una priorità alla regola. Le regole con numeri inferiori vengono eseguite per prime.
    * **Tipo di traffico**. Selezionare se la regola è per il traffico cloud privato, Internet o VPN (senza stato) o per un indirizzo IP pubblico (con stato).
    * **Protocollo**. Selezionare il protocollo coperto dalla regola (TCP, UDP o qualsiasi protocollo).
    * **Direzione**. Selezionare se la regola riguarda il traffico in ingresso o in uscita. È necessario definire regole separate per il traffico in ingresso e in uscita.
    * **Azione**. Selezionare l'azione da eseguire se la regola corrisponde (consentire o negare).
    * **Fonte**. Specificare le origini coperte dalla regola (blocco CIDR, interno o qualsiasi origine).
    * **Intervallo di porte di origine**. Specificare l'intervallo di porte soggette alla regola.
    * **Direzione**. Selezionare in ingresso o in uscita.
    * **Destinazione**. Specificare le destinazioni coperte dalla regola (blocco CIDR, interno o qualsiasi origine).
    * **Intervallo di porte di origine**. Specificare l'intervallo di porte soggette alla regola.

    ![Regola di aggiunta tabella firewall](media/firewall-rule-create.png)

3. Fare clic su **Fine** per salvare la regola e aggiungerla all'elenco di regole per la tabella firewall.

> [!IMPORTANT]
> Ogni tabella Del firewall può avere fino a 10 regole in ingresso e 20 regole in uscita. Questi limiti possono essere aumentati [contattando](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)il supporto .

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Collegare VLAN/subnet

Dopo aver definito una tabella firewall, è possibile specificare le subnet soggette alle regole nella tabella.

1. Nella pagina**Tabelle firewall** di **rete** > selezionare una tabella firewall.
2. Aprire la scheda **VLAN/subnet collegate.**
3. Fare clic su **Connetti a una VLAN/Subnet**.
4. Selezionare il cloud privato e VLAN. Vengono visualizzati il nome della subnet associata e il blocco CIDR.
5. Fare clic su **Submit**.
