<properties
	pageTitle="Azure AD Connect: Considerazioni speciali per le istanze | Microsoft Azure"
	description="Questa pagina contiene considerazioni speciali per le istanze di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Azure AD Connect: Considerazioni speciali per le istanze
Azure AD Connect si usa soprattutto con l'istanza di Azure AD e Office 365 disponibili in tutto il mondo. Ma esistono anche altre istanze e hanno requisiti diversi per gli URL e altre considerazioni speciali.

## Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) è un sovereign cloud gestito da un trustee dei dati tedesco.

Questo cloud è attualmente in fase di anteprima. Molte delle attività che normalmente gli utenti svolgono da soli, ad esempio la verifica dei domini, devono essere completate dall'operatore. Per altre informazioni su come partecipare all'anteprima, contattare il rappresentante Microsoft locale.

URL da aprire nel server proxy |
--- |
*.microsoftonline.de |
*.windows.net |
\+Elenchi di revoche dei certificati |

Quando si accede alla directory di Azure AD è necessario usare un account nel dominio onmicrosoft.de.

Funzionalità attualmente non presenti in Microsoft Cloud Germany:

- Azure AD Connect Health non è disponibile.
- Aggiornamenti automatici non è disponibile.
- Il writeback delle password non è disponibile.

## Cloud di Microsoft Azure per enti pubblici
Il [cloud di Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) è un cloud riservato al governo degli Stati Uniti.

È stato supportato da versioni precedenti di DirSync. Dalla build 1.1.180 di Azure AD Connect è supportata la prossima generazione del cloud. Questa generazione usa endpoint situati esclusivamente negli Stati Uniti e un elenco diverso di URL da aprire nel server proxy.

URL da aprire nel server proxy |
--- |
*.microsoftonline.com |
*.gov.us.microsoftonline.com |
\+Elenchi di revoche dei certificati |

Azure AD Connect non è in grado di rilevare automaticamente che la directory di Azure AD si trova nel cloud per enti pubblici. È necessario intraprendere le azioni seguenti quando si installa Azure AD Connect.

1. Avviare l'installazione di Azure AD Connect.
2. Non appena viene visualizzata la prima pagina in cui si accetta il contratto di licenza, non continuare ma lasciare che venga eseguita l'installazione guidata.
3. Avviare regedit e modificare la chiave del Registro di sistema `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` impostandola sul valore `2`.
4. Tornare all'installazione guidata di Azure AD Connect, accettare il contratto di licenza e continuare. Durante l'installazione, assicurarsi di usare il percorso di installazione della **configurazione personalizzata** e non l'installazione rapida. Continuare l'installazione come di consueto.

Funzionalità attualmente non presenti nel cloud di Microsoft Azure per enti pubblici:

- Azure AD Connect Health non è disponibile.
- Aggiornamenti automatici non è disponibile.
- Il writeback delle password non è disponibile.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->