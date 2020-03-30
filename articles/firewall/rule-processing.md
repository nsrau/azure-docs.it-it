---
title: Logica di elaborazione delle regole del Firewall di Azure
description: Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264778"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logica di elaborazione delle regole del Firewall di Azure
È possibile configurare le regole NAT, le regole di rete e le regole delle applicazioni in Firewall di Azure.You can configure NAT rules, network rules, and applications rules on Azure Firewall. Le regole vengono elaborate in base al tipo. 

> [!NOTE]
> Se si abilita il filtro basato sull'intelligence sulle minacce, tali regole hanno la priorità più alta e vengono sempre elaborate per prime. Il filtro di Threat-Intelligence può negare il traffico prima dell'elaborazione delle regole configurate. Per altre informazioni, vedere Filtro basato su minacce di Firewall di Azure.For more information, see [Azure Firewall threat-based filtering](threat-intel.md).

## <a name="outbound"></a>In uscita

### <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione

Se si configurano le regole di rete e le regole dell'applicazione, le regole di rete vengono applicate in ordine di priorità prima delle regole dell'applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Pertanto, se viene trovata una corrispondenza in una regola di rete, non vengono elaborate altre regole.  Se non è presente alcuna corrispondenza tra le regole di rete e se il protocollo è HTTP, HTTPS o MSSQL, il pacchetto viene quindi valutato dalle regole dell'applicazione in ordine di priorità. Se ancora non viene trovata alcuna corrispondenza, il pacchetto viene valutato in base alla [raccolta di regole di infrastruttura.](infrastructure-fqdns.md) Se non è ancora presente alcuna corrispondenza, il pacchetto viene rifiutato per impostazione predefinita.

## <a name="inbound"></a>In ingresso

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