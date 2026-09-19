# Istio

## Sem as restrições do Istio
- vamos criar alguns manifestos para simular o nosso lab!
    - pod attacker no namespace default
    - namespace production com a label do istio
    - criação de 2 ServiceAccount (frontend-sa e billing-sa)    
    - criação de um deployment de frontend    
    - criação de um service de frontend
    - criação de um deployment de billing
    - criação de um service de billing    
- Vamos simular um acesso não autorizado de outro namespace e com outro Service Account    
    - Temos um problema não é mesmo? Quem deveria conhecer o billing service é somente o frontend e com um Service Account especifico! Vamos corrigir isso!

## Com as restrições do Istio
- Vamos criar mais 2 manifestos:
    - criação de um PeerAuthentication
    - criação de um AuthorizationPolicy
- Vamos simular novamente?        
    - Agora sim esta tudo OK!!!