|  | **De point à site** | **De site à site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Services pris en charge par Azure** |Cloud Services et Virtual Machines |Cloud Services et Virtual Machines |[Liste des services](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Bandes passantes classiques** |En règle générale < 100 Mbit/s agrégés |En règle générale < 100 Mbit/s agrégés |50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 2 Gbit/s, 5 Gbit/s, 10 Gbit/s |
| **Protocoles pris en charge** |Secure Sockets Tunneling Protocol (SSTP) |IPsec |Connexion directe sur des VLAN, les technologies VPN des fournisseurs de services réseau (MPLS, VPLS,...) |
| **Routage** |RouteBased (dynamique) |Nous prenons en charge le routage basé sur des stratégies (statique) et basé sur un itinéraire (VPN de routage dynamique) |BGP |
| **Résilience de connexion** |actif / passif |actif / passif |actif / actif |
| **Cas d’utilisation classique** |Création de prototypes, scénarios de développement / test / labo pour les services cloud et les machines virtuelles |Scénarios de développement / test / labo et charges de travail de production à petite échelle pour les services cloud et les machines virtuelles |Accès à tous les services Azure (liste validée), charges de travail professionnelles et critiques, sauvegarde, Big Data, Azure sous la forme d'un site de récupération d'urgence |
| **CONTRAT SLA** |[CONTRAT SLA](https://azure.microsoft.com/support/legal/sla/) |[CONTRAT SLA](https://azure.microsoft.com/support/legal/sla/) |[CONTRAT SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Tarification** |[Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Tarification](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentation technique** |[Documentation sur la passerelle VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentation sur la passerelle VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **FORUM AUX QUESTIONS** |[FAQ sur la passerelle VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[FAQ sur la passerelle VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Forum Aux Questions ExpressRoute](../articles/expressroute/expressroute-faqs.md) |



<!--HONumber=Nov16_HO3-->


