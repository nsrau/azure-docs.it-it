---
title: Filtro geografico in un dominio per il servizio Frontdoor di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sui criteri di filtro geografico per il servizio Frontdoor di Azure
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 42697a57d39f4a34eee4866b67e2cde947db1ff5
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449259"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Filtro geografico in un dominio per lo sportello anteriore di Azure

Per impostazione predefinita, il front-end di Azure risponderà a tutte le richieste degli utenti, indipendentemente dalla località da cui provengono. In alcuni scenari può essere opportuno limitare l'accesso all'applicazione Web da parte di paesi o aree geografiche. Il servizio Web Application Firewall (WAF) nella porta anteriore consente di definire un criterio usando regole di accesso personalizzate per un percorso specifico nell'endpoint per consentire o bloccare l'accesso da paesi o aree geografiche specifiche. 

Un criterio WAF contiene un set di regole personalizzate. La regola è costituita da condizioni di corrispondenza, un'azione e una priorità. In una condizione di corrispondenza è possibile definire una variabile di corrispondenza, un operatore e un valore di corrispondenza. Per una regola di filtro geografico, è REMOTE_ADDR una variabile di corrispondenza, l'operatore è geomatch e il valore è un codice di due lettere paese/area di interesse. Per creare una regola di filtro geografico basato sul percorso, è possibile combinare una condizione geomatch e una condizione di corrispondenza della stringa REQUEST_URI.

È possibile configurare un criterio di filtro geografico per la porta anteriore usando [Azure PowerShell](front-door-tutorial-geo-filtering.md) o usando un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="countryregion-code-reference"></a>Informazioni di riferimento sui codici paese/area geografica

|Codice paese/area geografica | Nome paese/area geografica |
| ----- | ----- |
| AD | Andorra |
| AE | Emirati Arabi Uniti|
| AF | Afghanistan|
| AG | Antigua e Barbuda|
| AL | Albania|
| AM | Armenia|
| AO | Angola|
| AR | Argentina|
| AS | Isole Samoa americane|
| AT | Austria|
| AU | Australia|
| AZ | Azerbaigian|
| BA | Bosnia ed Erzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgio|
| BF | Burkina Faso|
| BG | Bulgaria|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasile|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Bielorussia|
| BZ | Belize|
| CA | Canada|
| CD | Congo, Repubblica democratica|
| CF | Repubblica centrafricana|
| CH | Svizzera|
| CI | Côte d'Ivoire (Costa d'Avorio)|
| CL | Cile|
| CM | Camerun|
| CN | Cina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Cipro|
| CZ | Repubblica ceca|
| DE | Germania|
| DK | Danimarca|
| DO | Repubblica dominicana|
| DZ | Algeria|
| EC | Ecuador|
| EE | Estonia|
| EG | Egitto|
| ES | Spagna|
| ET | Etiopia|
| FI | Finlandia|
| FJ | Figi|
| FM | Micronesia, stati di|
| VF | Francia|
| GB | Regno Unito|
| GE | Georgia|
| GF | Guayana francese|
| GH | Ghana|
| GN | Guinea|
| GP | Guadalupa|
| GR | Grecia|
| GT | Guatemala|
| GY | Guiana|
| HK | RAS di Hong Kong|
| HN | Honduras|
| HR | Croazia|
| HT | Haiti|
| HU | Ungheria|
| ID | Indonesia|
| IE | Irlanda|
| IL | Israele|
| IN | India|
| IQ | Iraq|
| IR | Iran, Repubblica islamica|
| IS | Islanda|
| IT | Italia|
| JM | Giamaica|
| JO | Giordania|
| JP | Giappone|
| KE | Kenya|
| KG | Kirghizistan|
| KH | Cambogia|
| KI | Kiribati|
| KN | Saint Kitts e Nevis|
| KP | Repubblica democratica popolare di Corea|
| KR | Repubblica di Corea|
| KW | Kuwait|
| KY | Isole Cayman|
| KZ | Kazakhstan|
| LA | Repubblica democratica del Laos|
| LB | Libano|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Lituania|
| LU | Lussemburgo|
| LV | Lettonia|
| LY | Libia |
| MA | Marocco|
| MD | Moldova, Repubblica|
| MG | Madagascar|
| MK | Macedonia del Nord|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolia|
| MO | RAS di Macao|
| MQ | Martinica|
| MR | Mauritania|
| MT | Malta|
| MV | Maldive|
| MW | Malawi|
| MX | Messico|
| MY | Malaysia|
| MZ | Mozambico|
| ND | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Paesi Bassi|
| NO | Norvegia|
| NP | Nepal|
| NR | Nauru|
| NZ | Nuova Zelanda|
| OM | Oman|
| PA | Panama|
| PE | Perù|
| PH | Filippine|
| PK | Pakistan|
| PL | Polonia|
| PR | Portorico|
| PT | Portogallo|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Riunione|
| RO | Romania|
| RS | Serbia|
| RU | Federazione russa|
| LS | Ruanda|
| SA | Arabia Saudita|
| SD | Sudan|
| SE | Svezia|
| SG | Singapore|
| SI | Slovenia|
| SK | Slovacchia|
| SN | Senegal|
| SO | Somalia|
| SR | Suriname|
| SS | Sudan del Sud|
| SV | El Salvador|
| SY | Repubblica araba siriana|
| SZ | Swaziland|
| TC | Isole Turks e Caicos|
| TG | Togo|
| TH | Thailandia|
| TN | Tunisia|
| TR | Turchia|
| TT | Trinidad e Tobago|
| TW | Taiwan|
| TZ | Tanzania, Repubblica del|
| UA | Ucraina|
| UG | Uganda|
| Stati Uniti | Stati Uniti|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent e Grenadine|
| VE | Venezuela|
| VG | Isole Vergini britanniche|
| VI | Isole Vergini americane|
| VN | Vietnam|
| ZA | Sud Africa|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni a caldo per [configurare un criterio WAF di filtro geografico](front-door-tutorial-geo-filtering.md).
