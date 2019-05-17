---
title: Copertura per la geocodifica in mappe di Azure | Microsoft Docs
description: Informazioni sulla copertura per la geocodifica in Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a5e5f4ab286289e223a2fe10ff8cf45f43309f04
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785948"
---
# <a name="azure-maps-geocoding-coverage"></a>Copertura per la geocodifica in Mappe di Azure

Quando si cerca una posizione con Mappe di Azure, il servizio di ricerca accetta i termini di ricerca e restituisce le coordinate di latitudine e longitudine, un processo denominato geocodifica. Tuttavia Mappe non ha lo stesso livello di informazioni e accuratezza per tutti i paesi e le aree. Usare questo articolo per determinare il tipo di posizioni che è possibile cercare in modo affidabile in ogni area. 

La possibilità per la geocodifica in un paese/area geografica è dipendente dal code coverage dei dati di viaggio e la precisione di geocodifica del servizio di geocodifica. Vengono utilizzate le categorizzazioni seguente specificare il livello di supporto per la geocodifica in ogni paese/area geografica.
* **Punti di indirizzo**: i dati degli indirizzi possono essere risolti in una coordinata di latitudine/longitudine all'interno del lotto di indirizzo (limite di proprietà). Talvolta definito come accurato in modo "Rooftop". Questo è il massimo livello di accuratezza disponibile per gli indirizzi. 
* **Numeri civici**: Gli indirizzi sono interpolati a una coordinata latitudine/longitudine sulla strada.
* **Livello stradale**: Gli indirizzi vengono risolti in base alla coordinata latitudine/longitudine della strada che contiene l'indirizzo. Il numero civico può non essere elaborato.
* **Livello città**: Sono supportati i nomi delle città.

## <a name="americas"></a>Americhe

| Paese/Area geografica                                       | Punti di indirizzo | Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antartide                                          |                 |                |              |      ✓     |          ✓         |
| Antigua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius e Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasile                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Cayman                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Cile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colombia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana, Repubblica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Falkland (Isole Malvine)                                    |                 |                |              |      ✓     |          ✓         |
| Guayana francese                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupa                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Giamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Messico                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Perù                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Portorico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts e Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre e Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent e Grenadine                    |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia del Sud e Sandwich Australi        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Altre isole americane del Pacifico                |                 |                |              |      ✓     |          ✓         |
| Stati Uniti d'America                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Vergini Britanniche                              |                 |                |              |      ✓     |          ✓         |
| Dati Isole Vergini americane                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asia/Pacifico

| Paese/Area geografica                                      | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americane                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Territorio britannico dell'Oceano Indiano                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambogia                                            |                 |                |              |      ✓     |          ✓         |
| Cina                                               |                 |                |              |      ✓     |          ✓         |
| Isola Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Isole Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Comore                                             |                 |                |              |      ✓     |          ✓         |
| Isole Cook                                        |                 |                |              |      ✓     |          ✓         |
| Figi                                                |                  |                |              |      ✓     |          ✓        |
| Polinesia Francese                                    |                 |                |              |      ✓     |          ✓         |
| Isole Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong - R.A.S.                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| India                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Giappone                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Corea del Sud                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| RAS di Macao                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaysia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronesia                                          |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nuova Caledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nuova Zelanda                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Isole Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Corea del Nord                                         |                 |                |              |      ✓     |          ✓         |
| Marianne Settentrionali                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Nuova Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Isole Paracelso                                     |                 |                |              |      ✓     |                    |
| Filippine                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Isole Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapore                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Salomone                                     |                 |                |              |      ✓     |          ✓         |
| Curili meridionali                                     |        ✓        |                |              |      ✓     |          ✓         |
| Isole Spratly                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thailandia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Isole Turks e Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Paese/Area geografica                                      | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaigian                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgio                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnia ed Erzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgaria                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bielorussia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croazia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cipro                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Repubblica ceca                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danimarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Fær Øer                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Germania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibilterra                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Isola di Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ungheria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isola di Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italia                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakhstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirghizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettonia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituania                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lussemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedonia del Nord                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Principato di Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Paesi Bassi                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvegia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polonia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portogallo                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Azzorre e Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Romania                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federazione russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovacchia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spagna                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Svezia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Svizzera                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tagikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turchia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ucraina                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Regno Unito                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Città del Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Medio Oriente e Africa

| Paese/Area geografica                                      | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrain                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Isola Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Repubblica Centrafricana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ciad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Repubblica del Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire (Costa d'Avorio)                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Congo, Repubblica democratica                    |                 |                |       ✓      |      ✓     |          ✓         |
| Gibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egitto                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guinea Equatoriale, Repubblica                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Terre Australi Francesi|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Iraq                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israele                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Giordania                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libano                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldive                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Marshall                                    |                 |                |              |      ✓     |          ✓         |
| Mauritania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marocco                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambico                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sant'Elena                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudita                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Sudafrica                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudan del Sud                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Siria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirati Arabi Uniti                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Yemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla geocodifica di Mappe di Azure, vedere le pagine di riferimento della funzione di [ricerca](https://docs.microsoft.com/rest/api/maps/search).

Vedere la [aree di copertura per il servizio del traffico di Mappe](traffic-coverage.md). 

