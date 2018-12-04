---
title: Copertura per la geocodifica in Mappe di Azure | Microsoft Docs
description: Informazioni sulla copertura per la geocodifica in Mappe di Azure
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 664d52a96f1e724502f82f5f72567f87c74e716b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444257"
---
# <a name="azure-maps-geocoding-coverage"></a>Copertura per la geocodifica in Mappe di Azure

Quando si cerca una posizione con Mappe di Azure, il servizio di ricerca accetta i termini di ricerca e restituisce le coordinate di latitudine e longitudine, un processo denominato geocodifica. Tuttavia Mappe non ha lo stesso livello di informazioni e accuratezza per tutti i paesi e le aree. Usare questo articolo per determinare il tipo di posizioni che è possibile cercare in modo affidabile in ogni area. 

La capacità di geocodifica in un paese dipende dalla copertura dei dati stradali e dalla precisione di geocodifica del servizio di geocodifica. Vengono utilizzate le seguenti categorizzazioni per specificare il livello di supporto per la geocodifica in ogni paese.
* **Punti di indirizzo**: i dati degli indirizzi possono essere risolti in una coordinata di latitudine/longitudine all'interno del lotto di indirizzo (limite di proprietà). Talvolta definito come accurato in modo "Rooftop". Questo è il massimo livello di accuratezza disponibile per gli indirizzi. 
* **Numeri civici**: Gli indirizzi sono interpolati a una coordinata latitudine/longitudine sulla strada.
* **Livello stradale**: Gli indirizzi vengono risolti in base alla coordinata latitudine/longitudine della strada che contiene l'indirizzo. Il numero civico può non essere elaborato.
* **Livello città**: Sono supportati i nomi delle città.

## <a name="americas"></a>Americhe

| Region             | Punti di indirizzo | Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antartide                                          |                 |                |              |      ✓     |          ✓         |
| Antigua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius e Saba|                 |                |              |      ✓     |          ✓         |
| Brasile                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Cayman                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Cile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Isola Clipperton                                   |                 |                |              |      ✓     |                    |
| Colombia                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana, Repubblica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Falkland                                    |                 |                |              |      ✓     |          ✓         |
| Guayana francese                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupa|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana        |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Giamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Messico                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Perù                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Portorico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts e Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre e Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent e Grenadine                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia del Sud e Sandwich Australi        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Altre isole minori degli Stati Uniti                |                 |                |              |      ✓     |          ✓         |
| Stati Uniti d'America                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Vergini britanniche                            |                 |                |              |      ✓     |          ✓         |
| Isole Vergini americane                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asia/Pacifico

| Region             | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Isole Samoa americane                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Territorio britannico dell'oceano Indiano                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambogia                                            |                 |                |              |      ✓     |          ✓         |
| Cina |                 |                |              |      ✓     |          ✓         |
| Isola Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Isole Cocos (Keeling)|                 |                |              |      ✓     |          ✓         |
| Comore|                 |                |              |      ✓     |          ✓         |
| Isole Cook                                        |                 |                |              |      ✓     |          ✓         |
| Figi |                 |                |              |      ✓     |          ✓         |
| Polinesia francese                                    |                 |                |              |      ✓     |          ✓         |
| Isole Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| RAS di Hong Kong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| India       |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Giappone                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao - R.A.S.                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaysia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronesia |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal|                 |                |              |      ✓     |          ✓         |
| Nuova Caledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nuova Zelanda                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Isola di Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Corea del Nord                                         |                 |                |              |      ✓     |          ✓         |
| Marianne Settentrionali                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Papua Nuova Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Isole Paracelso                                     |                 |                |              |      ✓     |                    |
| Filippine                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Isole Samoa                                                |                 |                |              |      ✓     |          ✓         |
| Isole Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapore                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Salomone                                     |                 |                |              |      ✓     |          ✓         |
| Corea del Sud                                         |                 |                |              |      ✓     |          ✓         |
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
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna|                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Region             | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |                 |                |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaigian                                          |                 |                |              |      ✓     |          ✓         |
| Belgio                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnia ed Erzegovina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bulgaria                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Bielorussia|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croazia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cipro                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Repubblica ceca                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danimarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isole Fær Øer                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgia                                             |                 |                |              |      ✓     |          ✓         |
| Germania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibilterra                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Isola di Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ungheria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Islanda                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda, Repubblica                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isola di Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italia                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakhstan                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirghizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettonia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituania                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Lussemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedonia, ex Repubblica Jugoslava di                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Principato di Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Paesi Bassi                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvegia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polonia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portogallo                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Romania                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federazione russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovacchia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spagna                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Svezia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Svizzera                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tagikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turchia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ucraina                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Regno Unito                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Città del Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Medio Oriente e Africa

| Region             | Punti di indirizzo |Numeri di abitazione | Livello rete stradale | Livello città | Punti d'interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrain                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Isola Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde|                 |                |       ✓      |      ✓     |          ✓         |
| Repubblica centrafricana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Costa d'Avorio                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Congo, Repubblica democratica                        |                 |                |       ✓      |      ✓     |          ✓         |
| Gibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egitto                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guinea Equatoriale, Repubblica                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Terre australi francesi|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Iraq                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israele                                              |                 |                |              |      ✓     |          ✓         |
| Giordania                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libano                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia|                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldive |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Isole Marshall |                 |                |              |      ✓     |          ✓         |
| Mauritania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marocco                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambico                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sant'Elena                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudita                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Sud Africa                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudan del Sud                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Siria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe, Repubblica democratica di       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirati Arabi Uniti                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cisgiordania                                           |                 |                |              |      ✓     |          ✓         |
| Yemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla geocodifica di Mappe di Azure, vedere le pagine di riferimento della funzione di [ricerca](https://docs.microsoft.com/rest/api/maps/search).

Vedere la [aree di copertura per il servizio del traffico di Mappe](traffic-coverage.md). 

