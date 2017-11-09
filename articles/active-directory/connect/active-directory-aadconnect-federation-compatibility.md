---
title: "Elenco di compatibilità di federazione di Azure AD"
description: "Questa pagina include provider di identità non Microsoft che possono essere usati per implementare l'accesso Single Sign-On."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: bce5867017647764546d872d97943d5d4f01f2d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Elenco di compatibilità di federazione di Azure AD
Azure Active Directory offre la sicurezza di accesso Single Sign-On e di applicazione avanzata per Office 365 e altri Microsoft Online Services per implementazioni ibride o solo cloud senza richiedere alcuna soluzione non Microsoft. Analogamente alla maggior parte dei Microsoft Online Services, Office 365 è integrato con Azure Active Directory per i servizi di directory, l'autenticazione e l'autorizzazione. Azure Active Directory offre anche l'accesso Single Sign-On a migliaia di applicazioni SaaS e di applicazioni Web locali. Per le applicazioni SaaS supportate, vedere la raccolta di applicazioni Azure Active Directory.

Per le organizzazioni che hanno investito in soluzioni di federazione non Microsoft, questo argomento include le istruzioni per la configurazione dell'accesso Single Sign-On per gli utenti Active Directory di Windows Server con Microsoft Online Services tramite i provider di identità non Microsoft inclusi nell'elenco di compatibilità di federazione di Azure Active Directory riportato di seguito. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/), una terza parte, ha testato per conto di Microsoft queste esperienze di accesso Single Sign-On usando provider di identità non Microsoft in più casi d'uso comuni con Azure Active Directory.

Per informazioni su come ottenere il provider di identità di terze parti elencato di seguito, contattare Oxford Computer Group all'indirizzo [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Oxford Computer Group si è limitata a testare la funzionalità di federazione di questi scenari di accesso Single Sign-On. Oxford Computer Group, in questi scenari di accesso Single Sign-On, non ha eseguito alcun test sui componenti di sincronizzazione, autenticazione a due fattori e così via.
> 
> Anche l'uso dell'accesso tramite ID alternativo all'UPN non è stato testato in questo programma.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP con Access Policy Manager BIG-IP versione 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [Autenticazione composita di DigitalPersona](#digitalpersona-composite-authentication)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation versione 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Virtual Identity Server Federation Services di Optimal IDM](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&amp;go 5.3](#signgo-53) 
* [Online Service Gate di SoftBank Technology](#softbank)
* [VMware Workspace One](#vmware-workspace-one)



> [!IMPORTANT]
> Poiché si tratta di prodotti di terze parti, Microsoft non fornisce alcun supporto per la distribuzione, la configurazione, la risoluzione dei problemi, le procedure consigliate, nonché informazioni su problemi e domande su questi provider di identità. Per supporto e domande relative ai provider di identità, contattare direttamente le terze parti supportate.
> 
> Per testare l'interoperabilità di questi provider di terze parti con i servizi cloud Microsoft sono stati usati solo i protocolli WS-Federation e WS-Trust. Il test non prevedeva l'uso del protocollo SAML.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |None |
| Applicazioni moderne con ADAL, ad esempio Office 2016 |Supportato |Nessuna |

Per altre informazioni sull'uso di Azure Active Directory con AD FS, vedere [Configurazione della federazione con AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Per altre informazioni sull'uso di Azure Active Directory con la sincronizzazione delle password, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni, vedere [AuthAnvil Single Sign On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-) (Single Sign-On AuthAnvil).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP con Access Policy Manager BIG-IP versione 11.3x – 11.6x

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Non supportato |Non supportato |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |None |

Per altre informazioni su BIG-IP Access Policy Manager, vedere [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

Per ottenere le istruzioni di BIG-IP Access Policy Manager relative alla configurazione di questo servizio token di sicurezza per offrire l'accesso Single Sign-On agli utenti di Active Directory, scaricare il file con estensione pdf da [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su BitGlass, vedere [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>CA Secure Cloud

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su CA Secure Cloud, vedere il sito [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 cumulativo versione 4

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su CA SiteMinder, vedere [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Controllo di accesso client non supportato |

Per altre informazioni su Centrify, vedere [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su Dell One Identity Cloud Access Manager, vedere [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Per istruzioni su come configurare questo servizio token di sicurezza per offrire l'accesso Single Sign-On agli utenti di Office 365, vedere [How To Configure Microsoft Office 365](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) (Come configurare Microsoft Office 365). 

## <a name="digitalpersona-composite-authentication"></a>Autenticazione composita di DigitalPersona  

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata|
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata|
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni, vedere [DigitalPersona Composite Authentication](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf) (Autenticazione composita di DigitalPersona).


## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su IBM Tivoli Federated Identity Manager, vedere [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517) (IBM Security Access Manager per applicazioni Microsoft).

## <a name="icewall-federation-version-30"></a>IceWall Federation versione 3.0

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su IceWall Federation, vedere [IceWall Federation Version 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) (IceWall Federation Versione 3.0) e [IceWall Federation with Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html) (IceWall Federation con Office 365).

## <a name="memority"></a>Memority

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni sull'uso di Memority, vedere [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None|
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None|
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni, vedere [NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |Per Autenticazione integrata di Windows è richiesta la configurazione di altri server Web e dell'applicazione Okta. |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |Autenticazione integrata di Windows |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su Okta, vedere [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |Autenticazione integrata di Windows |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |Autenticazione integrata di Windows |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su OneLogin, vedere [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Virtual Identity Server Federation Services di Optimal IDM

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |Autenticazione integrata di Windows |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |

Per altre informazioni sui criteri di accesso client, vedere [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Limitare l'accesso ai servizi di Office 365 in base alla posizione del client).





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |None |

Per le istruzioni di PingFederate sulla configurazione di questo servizio token di sicurezza per offrire l'accesso Single Sign-On agli utenti di Active Directory, vedere uno dei seguenti articoli: 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |Autenticazione integrata di Windows |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su RadiantOne CFS, vedere il sito [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni, vedere [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On: 

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |None |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |None |

Per altre informazioni su SecureAuth, vedere il sito di [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Sign&go 5.3

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |Contratti Kerberos supportati |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |None |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |None |

Sign&go 5.3 supporta l'autenticazione Kerberos tramite la configurazione di un contratto Kerberos.  Per ottenere assistenza su questa configurazione, contattare Ilex o visualizzare la guida all'installazione [Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="softbank-technology-online-service-gate"></a>Online Service Gate di SoftBank Technology

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni su Online Service Gate di SoftBank Technology, vedere [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/)

## <a name="vmware-workspace-one"></a>VMware Workspace One

Di seguito è riportata la matrice di supporto dello scenario per questa esperienza di accesso Single Sign-On:

| Client | Supporto | Eccezioni |
| --- | --- | --- |
| Client basati sul Web, ad esempio Exchange Web Access e SharePoint Online |Supportato |L'autenticazione integrata di Windows non è supportata |
| Applicazioni rich client, ad esempio Lync, Sottoscrizione Office, CRM |Supportato |L'autenticazione integrata di Windows non è supportata |
| Client di posta elettronica, ad esempio Outlook e ActiveSync |Supportato |Nessuna |

Per altre informazioni, vedere [VMware Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf)

