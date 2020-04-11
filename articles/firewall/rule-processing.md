---
title: Logica di elaborazione delle regole del Firewall di Azure
description: Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113446"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logica di elaborazione delle regole del Firewall di Azure
È possibile configurare le regole NAT, le regole di rete e le regole delle applicazioni in Firewall di Azure.You can configure NAT rules, network rules, and applications rules on Azure Firewall. Le raccolte di regole vengono elaborate in base al tipo di regola in ordine di priorità, con numeri inferiori a numeri più alti da 100 a 65.000.Rule collections are processed according to the rule type in priority order, lower numbers to higher numbers from 100 to 65,000. Il nome di una raccolta regole può avere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero e terminare con una lettera, un numero o un segno di sottolineatura. La lunghezza massima del nome è 80 caratteri.

È consigliabile distanziare inizialmente i numeri di priorità della raccolta di regole in 100 incrementi (100, 200, 300 e così via), in modo da avere spazio per aggiungere altre raccolte di regole, se necessario.

> [!NOTE]
> Se si abilita il filtro basato sull'intelligence sulle minacce, tali regole hanno la priorità più alta e vengono sempre elaborate per prime. Il filtro di Threat-Intelligence può negare il traffico prima dell'elaborazione delle regole configurate. Per altre informazioni, vedere Filtro basato su minacce di Firewall di Azure.For more information, see [Azure Firewall threat-based filtering](threat-intel.md).

## <a name="outbound-connectivity"></a>Connettività in uscita

### <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione

Se si configurano le regole di rete e le regole dell'applicazione, le regole di rete vengono applicate in ordine di priorità prima delle regole dell'applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Pertanto, se viene trovata una corrispondenza in una regola di rete, non vengono elaborate altre regole.  Se non è presente alcuna corrispondenza tra le regole di rete e se il protocollo è HTTP, HTTPS o MSSQL, il pacchetto viene quindi valutato dalle regole dell'applicazione in ordine di priorità. Se ancora non viene trovata alcuna corrispondenza, il pacchetto viene valutato in base alla [raccolta di regole di infrastruttura.](infrastructure-fqdns.md) Se non è ancora presente alcuna corrispondenza, il pacchetto viene rifiutato per impostazione predefinita.

## <a name="inbound-connectivity"></a>Connettività in ingresso

### <a name="nat-rules"></a>Regole NAT

La connettività Internet in ingresso può essere abilitata configurando DNAT (Destination Network Address Translation) come descritto in [Esercitazione: Filtrare il traffico in ingresso con Azure Firewall DNAT usando il portale](tutorial-firewall-dnat.md)di Azure . Le regole NAT vengono applicate in priorità prima delle regole di rete. Se viene trovata una corrispondenza, viene aggiunta in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito.

Le regole dell'applicazione non vengono applicate per le connessioni in ingresso. Pertanto, se si desidera filtrare il traffico HTTP/S in ingresso, è necessario utilizzare Web Application Firewall (WAF). Per altre informazioni, vedere [Che cos'è il firewall delle applicazioni Web di Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Esempi

Negli esempi seguenti vengono illustrati i risultati di alcune di queste combinazioni di regole.

### <a name="example-1"></a>Esempio 1

La connessione a google.com è consentita a causa di una regola di rete corrispondente.

**Regola di rete**

- Azione: Consenti


|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Consenti Web     |TCP|Indirizzo IP|*|Indirizzo IP|*|80,443

**Regola di applicazione**

- Azione: Nega

|name  |Tipo di origine  |Source (Sorgente)  |Protocollo:Porta|FQDN di destinazione|
|---------|---------|---------|---------|----------|----------|
|Nega-google     |Indirizzo IP|*|http:80,https:443 (informazioni in base al sito web)|google.com

**Risultato**

La connessione a google.com è consentita perché il pacchetto corrisponde alla regola di rete *Allow-web.* L'elaborazione delle regole si interrompe a questo punto.

### <a name="example-2"></a>Esempio 2

Il traffico SSH viene negato perché una priorità più alta *Nega* raccolta regole di rete lo blocca.

**Raccolta regole di rete 1**

- Nome: Allow-collection
- Priorità: 200
- Azione: Consenti

|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Consenti-SSH     |TCP|Indirizzo IP|*|Indirizzo IP|*|22

**Raccolta regole di rete 2**

- Nome: Deny-collection
- Priorità: 100
- Azione: Nega

|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Nega-SSH     |TCP|Indirizzo IP|*|Indirizzo IP|*|22

**Risultato**

Le connessioni SSH vengono negate perché una raccolta di regole di rete con priorità più alta la blocca. L'elaborazione delle regole si interrompe a questo punto.

## <a name="rule-changes"></a>Modifiche alle regole

Se si modifica una regola per negare il traffico consentito in precedenza, tutte le sessioni esistenti pertinenti vengono eliminate.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).