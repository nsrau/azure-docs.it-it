
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Domande frequenti: hosting della zona ARPA in DNS di Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>È possibile ospitare zone ARPA per i blocchi IP assegnati dall’ISP sul DNS di Azure?

Sì. L'hosting delle zone ARPA per gli intervalli di IP in DNS di Azure è completamente supportato.

È sufficiente [creare la zona in DNS di Azure](../articles/dns/dns-getstarted-create-dnszone.md), quindi collaborare con l'ISP per [delegare la zona](../articles/dns/dns-domain-delegation.md).  Sarà quindi possibile gestire i record PTR per ogni ricerca inversa come per gli altri tipi di record.

È anche possibile [importare una zona di ricerca inversa esistente usando l'interfaccia della riga di comando di Azure](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Quanto costa l'hosting della zona ARPA?

L'hosting della zona ARPA per il blocco di IP assegnati dall'ISP in DNS di Azure viene addebitato alle [tariffe standard di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>È possibile ospitare zone ARPA per indirizzi IPv4 e IPv6 in DNS di Azure?

Sì.
