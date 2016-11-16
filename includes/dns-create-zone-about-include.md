Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio è necessario creare una zona DNS. Qualsiasi record DNS creato per un particolare dominio si troverà all'interno di una zona DNS per il dominio.

Ad esempio, il dominio "contoso.com" può contenere una serie di record DNS, come "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito Web).

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>Informazioni sui nomi delle zone DNS
* Il nome della zona deve essere univoco all'interno del gruppo di risorse e la zona non deve esistere già, altrimenti l'operazione non riesce.
* Lo stesso nome di zona può essere usato nuovamente in un gruppo di risorse diverso o in un'altra sottoscrizione Azure.
* Se più zone condividono lo stesso nome, a ogni istanza verranno assegnati diversi indirizzi del server dei nomi e può essere delegata solo un'istanza dal dominio padre. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](../articles/dns/dns-domain-delegation.md).


<!--HONumber=Nov16_HO2-->


