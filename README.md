# Imagens Docker para PHP

Este repositório contém imagens Docker para diferentes versões do PHP (8.1 a 8.5), incluindo configurações otimizadas para desenvolvimento de aplicações Laravel. As imagens são organizadas em pastas separadas por versão e tipo de configuração.

## Estrutura do Projeto

- `php81/`, `php82/`, `php83/`, `php84/`, `php85/`: Imagens básicas do PHP para cada versão.
- `php81-laravel/`, `php82-laravel/`, etc.: Imagens específicas para aplicações Laravel, com subpastas:
  - `dev/`: Configurações para ambiente de desenvolvimento.
  - `prod/`: Configurações para ambiente de produção (mais leves).

Cada pasta contém um `Dockerfile` e outros arquivos de configuração necessários, como `php.ini`, `supervisord.ini`, etc.

## Como Usar

1. Navegue até a pasta da versão e configuração desejada. Por exemplo, para PHP 8.1 com Laravel em desenvolvimento:
   ```
   cd php81-laravel/dev
   ```

2. Construa a imagem Docker:
   ```
   docker build -t minha-imagem-php .
   ```

3. Execute um container com a imagem:
   ```
   docker run -it --rm minha-imagem-php
   ```

Para aplicações Laravel, você pode integrar essas imagens em um `docker-compose.yml` para orquestrar serviços como banco de dados, web server, etc.

## Avisos Importantes

**Essas imagens são destinadas exclusivamente ao desenvolvimento.** Elas incluem uma ampla gama de extensões PHP instaladas para facilitar o desenvolvimento e testes, mas muitas dessas extensões podem ser desnecessárias para a maioria das aplicações e podem abrir brechas de segurança se usadas em produção.

Ao colocar a aplicação em produção, o desenvolvedor deve gerar sua própria imagem Docker contendo **apenas as extensões necessárias** para a sua aplicação específica. Isso reduz o tamanho da imagem, melhora a performance e aumenta a segurança, evitando vulnerabilidades em extensões não utilizadas.

Para criar uma imagem de produção personalizada, edite o `Dockerfile` correspondente removendo extensões desnecessárias e ajustando as configurações conforme as necessidades da aplicação.

## Contribuição

Sinta-se à vontade para contribuir com melhorias, correções ou novas versões. Abra uma issue ou pull request no repositório.

## Licença

Este projeto está licenciado sob a [MIT License](LICENSE).