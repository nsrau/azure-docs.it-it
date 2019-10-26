---
title: Copertura per il rendering in Mappe di Azure | Microsoft Docs
description: Informazioni sulla copertura per il rendering in Mappe di Azure
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934283"
---
# <a name="azure-maps-render-coverage"></a>Copertura per il rendering in Mappe di Azure

Mappe di Azure usa sia i riquadri raster che i riquadri vettoriali per creare le mappe. Alla risoluzione minima, il mondo è incluso in un unico riquadro. Alla risoluzione massima, un singolo riquadro rappresenta 38 metri quadrati. Quando si fa zoom avanti su una mappa, è quindi possibile visualizzare sempre più dettagli di continenti, aree, città e singole strade. Per altre informazioni, vedere [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Mappe non ha tuttavia lo stesso livello di informazioni e accuratezza per tutte le aree. Le tabelle seguenti forniscono informazioni sul livello del dettaglio di rendering previsto per ogni area.

## <a name="legend"></a>Legenda

| Simbolo | Significato |
|--------|---------|
| ✓ | L'area è rappresentata con dati dettagliati.   |
| Ø | L'area è rappresentata con dati semplificati. |


## <a name="africa"></a>Africa 


| Paese/Area geografica | Riquadri raster unificati | Riquadri vettoriali unificati |
| ------ | :------------------: | :------------------: |
| Algeria                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Camerun                         | ✓ | ✓ |
| Repubblica Centrafricana         | ✓ | Ø |
| Chad                             | ✓ | Ø |
| Comore                          | ✓ | Ø |
| Repubblica democratica del Congo | ✓ | ✓ |
| Côte d'Ivoire (Costa d'Avorio)                    | ✓ | Ø |
| Gibuti                         | ✓ | Ø |
| Egitto                            | ✓ | ✓ |
| Guinea equatoriale                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Etiopia                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Bissau                    | ✓ | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libia                            | ✓ | Ø |
| Madagascar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritania                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marocco                          | ✓ | ✓ |
| Mozambico                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| Sant'Elena, Ascensione e Tristan da Cunha | ✓ | Ø |
| São Tomé e Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Sudafrica                     | ✓ | ✓ |
| Sudan del Sud                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Swaziland                        | ✓ | ✓ |
| Repubblica unita di Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisia                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Americhe

| Paese/Area geografica | Riquadri raster unificati | Riquadri vettoriali unificati |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua e Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Stato plurinazionale di Bolivia | ✓ | ✓ |
| Bonaire, Sint Eustatius e Saba | ✓ | ✓ |
| Brasile                    | ✓ | ✓ |
| Canada                    | ✓ | ✓ |
| Isole Cayman            | ✓ | ✓ |
| Cile                     | ✓ | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Cuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Repubblica Domenicana        | ✓ | ✓ |
| Ecuador                   | ✓ | ✓ |
| Isole Falkland | ✓ | ✓ |
| Guayana francese             | ✓ | ✓ |
| Groenlandia                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadalupa                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guiana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Giamaica                   | ✓ | ✓ |
| Martinica                | ✓ | ✓ |
| Messico                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Marianne Settentrionali  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Perù                      | ✓ | ✓ |
| Portorico               | ✓ | ✓ |
| Quebec (Canada)           | ✓ | ✓ |
| Saint Barthélemy          | ✓ | ✓ |
| Saint Christopher e Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint Martin (francese)     | ✓ | ✓ |
| Saint Pierre e Miquelon | ✓ | ✓ |
| Saint Vincent e Grenadine | ✓ | ✓ |
| Sint Maarten (olandese)      | ✓ | ✓ |
| Georgia del Sud e Sandwich Australi | ✓ | ✓ |
| Suriname                  | ✓ | ✓ |
| Trinidad e Tobago       | ✓ | ✓ |
| Isole Turks e Caicos  | ✓ | ✓ |
| Stati Uniti             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Isole Vergini britanniche   | ✓ | ✓ |
| Isole Vergini Americane      | ✓ | ✓ |

## <a name="asia"></a>Asia 

| Paese/Area geografica | Riquadri raster unificati | Riquadri vettoriali unificati |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhutan                    |   | Ø |
| Territorio britannico dell'Oceano indiano |   | Ø |
| Brunei                    | ✓ | ✓ |
| Cambogia                  |   | Ø |
| Cina                     |   | Ø |
| Isole Cocos (Keeling)   |   | Ø |
| Repubblica democratica popolare di Corea |   | Ø |
| RAS di Hong Kong             | ✓ | ✓ |
| India                     | Ø | ✓ | 
| Indonesia                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Iraq                      | ✓ | ✓ |
| Israele                    |   | ✓ |
| Giappone                     |   | Ø |
| Giordania                    | ✓ | ✓ |
| Kazakhstan                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirghizistan                |   | Ø |
| Repubblica democratica popolare del Laos |   | Ø |
| Libano                   | ✓ | ✓ |
| RAS di Macao                 | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Maldive                  |   | Ø |
| Mongolia                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filippine               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| Repubblica di Corea         | ✓ | Ø |
| Arabia Saudita              | ✓ | ✓ |
| Isole Senkaku/Diaoyutai           |   | ✓ |
| Singapore                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Repubblica araba siriana      |   | Ø |
| Taiwan                    | ✓ | ✓ |
| Tagikistan                |   | Ø |
| Tailandia                  | ✓ | ✓ |
| Timor Est               |   | Ø |
| Turkmenistan              |   | Ø |
| Emirati Arabi Uniti      | ✓ | ✓ |
| Altre isole americane del Pacifico |   | Ø |
| Uzbekistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Yemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceania

| Paese/Area geografica | Riquadri raster unificati | Riquadri vettoriali unificati |
| ------ | :------------------: | :------------------: |
| Isole Samoa americane            |   | ✓ |
| Australia                 | ✓ | ✓ |
| Isole Cook              |   | Ø |
| Figi                      |   | Ø |
| Polinesia francese          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Isole Marshall          |   | Ø |
| Micronesia                |   | Ø |
| Nauru                     |   | Ø |
| Nuova Caledonia             |   | Ø |
| Nuova Zelanda               | ✓ | ✓ |
| Niue                      |   | Ø |
| Isola di Norfolk            |   | Ø |
| Palau                     |   | Ø |
| Papua Nuova Guinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Isole Samoa                     |   | Ø |
| Isole Salomone           |   | Ø|
| Tokelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis e Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Paese/Area geografica | Riquadri raster unificati | Riquadri vettoriali unificati |
| ------ | :------------------: | :------------------: |
| Albania                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenia                   | ✓ | Ø |
| Austria                   | ✓ | ✓ |
| Azerbaigian                | ✓ | Ø |
| Bielorussia                   | Ø | ✓ |
| Belgio                   | ✓ | ✓ |
| Bosnia ed Erzegovina        | ✓ | ✓ |
| Bulgaria                  | ✓ | ✓ |
| Croazia                   | ✓ | ✓ |
| Cipro                    | ✓ | ✓ |
| Repubblica Ceca            | ✓ | ✓ |
| Danimarca                   | ✓ | ✓ |
| Estonia                   | ✓ | ✓ |
| Fær Øer             | ✓ | Ø |
| Finlandia                   | ✓ | ✓ |
| Francia                    | ✓ | ✓ |
| Georgia                   | ✓ | Ø |
| Germania                   | ✓ | ✓ |
| Gibilterra                 | ✓ | ✓ |
| Grecia                    | ✓ | ✓ |
| Isola di Guernsey                  | ✓ | ✓ |
| Ungheria                   | ✓ | ✓ |
| Islanda                   | ✓ | ✓ |
| Irlanda                   | ✓ | ✓ |
| Isola di Man               | ✓ | ✓ |
| Italia                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lettonia                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Lituania                 | ✓ | ✓ |
| Lussemburgo                | ✓ | ✓ |
| Macedonia del Nord           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Paesi Bassi               | ✓ | ✓ |
| Norvegia                    | ✓ | ✓ |
| Polonia                    | ✓ | ✓ |
| Portogallo                  | ✓ | ✓ |
| Romania                   | ✓ | ✓ |
| Federazione russa        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbia                    | ✓ | ✓ |
| Slovacchia                  | ✓ | ✓ |
| Slovenia                  | ✓ | ✓ |
| Curili meridionali           | ✓ | ✓ |
| Spagna                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Svezia                    | ✓ | ✓ |
| Svizzera               | ✓ | ✓ |
| Turchia                    | ✓ | ✓ |
| Ucraina                   | ✓ | ✓ |
| Regno Unito            | ✓ | ✓ |
| Città del Vaticano              | ✓ | ✓ |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul rendering di Mappe di Azure, vedere [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Informazioni sulle [aree di copertura per il servizio di routing di Mappe](routing-coverage.md). 