---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Replicon | Microsoft Docs'
description: Apprenez à utiliser Replicon avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-replicon"></a>Didacticiel : Intégration d’Azure Active Directory à Replicon
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Replicon. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Replicon

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Replicon pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Replicon (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Replicon
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")

## <a name="enabling-the-application-integration-for-replicon"></a>Activation de l’intégration d’application pour Replicon
Cette section décrit l’activation de l’intégration d’application pour Replicon.

### <a name="to-enable-the-application-integration-for-replicon,-perform-the-following-steps:"></a>Pour activer l’intégration d’application pour Replicon, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-replicon-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-replicon-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-replicon-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **Replicon**.
   
   ![Galerie d’applications](./media/active-directory-saas-replicon-tutorial/IC777799.png "Application gallery")
7. Dans le volet de résultats, sélectionnez **Replicon**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Replicon avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, puis dans la page d’intégration d’application **Replicon**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configure single sign-on")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Replicon**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configure single sign-on")
3. Dans la page **Configurer l’URL de l’application** , procédez comme suit :
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configure app URL")
   
   1. Dans la zone de texte **Replicon Sign On URL**, saisissez l’URL de votre client Replicon (par exemple : *https://na2.replicon.com/company/saml2/sp-sso/post*).
   2. Dans la zone de texte **Replicon Reply URL**, entrez votre URL Replicon **AssertionConsumerService** (par exemple, *https://global.replicon.com/!/saml2/company/sso/post*).  
      
      > [!NOTE]
      > Vous pouvez obtenir l’URL à partir des métadonnées Replicon à :         **https://global.replicon.com/!/saml2/\<CléDeVotreEntreprise>\>**.
      > 
      > 
   3. Cliquez sur **Suivant**
4. Dans la page **Configurer l’authentification unique sur Replicon**, cliquez sur **Télécharger les métadonnées**, puis enregistrez les métadonnées sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configure single sign-on")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Replicon en tant qu’administrateur.
6. Procédez comme suit pour configurer SAML 2.0 :
   
   ![Activer l’authentification SAML](./media/active-directory-saas-replicon-tutorial/IC777805.png "Enable SAML authentication")
   
   1. Pour afficher la boîte de dialogue **EnableSAML Authentication2**, ajoutez ce qui suit à votre URL, après la clé de votre entreprise :  
      **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
      Voici une illustration du schéma de l’URL complète :  
      **https://na2.replicon.com/\<CléDeVotreEntreprise\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. Cliquez sur **+** pour développer la section **v20Configuration**.
   3. Cliquez sur **+** pour développer la section **metaDataConfiguration**.
   4. Cliquez sur **Choose File** pour sélectionner votre fichier XML de métadonnées de fournisseur d’identité, puis cliquez sur **Submit**.
7. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Replicon, les utilisateurs d’Azure AD doivent être approvisionnés dans Replicon.  
Dans le cas de Replicon, l’approvisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning,-perform-the-following-steps:"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :
1. Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise Replicon en tant qu’administrateur.
2. Accédez à **Administration \> Users**.
   
   ![Utilisateurs](./media/active-directory-saas-replicon-tutorial/IC777806.png "Users")
3. Cliquez sur **+Add User**.
   
   ![Ajouter un utilisateur](./media/active-directory-saas-replicon-tutorial/IC777807.png "Add User")
4. Dans la section **User Profile** , procédez comme suit :
   
   ![User Profile](./media/active-directory-saas-replicon-tutorial/IC777808.png "User profile")
   
   1. Dans la zone de texte **Login Name** , tapez l’adresse de messagerie Azure AD de l’utilisateur Azure AD que vous souhaitez approvisionner.
   2. Pour **Authentication Type**, sélectionnez **SSO**.
   3. Dans la zone de texte **Department** , tapez le département de l’utilisateur.
   4. Pour **Employee Type**, sélectionnez **Administrator**.
   5. Cliquez sur **Save User Profile**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Replicon, pour approvisionner des comptes utilisateur AAD.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-replicon,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à Replicon, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **Replicon**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-replicon-tutorial/IC777809.png "Assign users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-replicon-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


