# RH Performa — Central de Módulos

Plataforma modular de softwares de Recursos Humanos da **RH Performa**. A Central é uma aplicação web hospedada no GitHub Pages, acessível pelo navegador ou pelo aplicativo desktop para Windows (Electron).

## Módulos

| Módulo | Situação atual | Comportamento |
| --- | --- | --- |
| Cargos e Salários | Disponível | Licença ativa: acesso ao módulo. Sem licença ativa: acesso à página de vendas. |
| Recrutamento e Seleção | Em desenvolvimento | Exibido na Central, mas indisponível. |
| DISC | Em desenvolvimento | Exibido na Central, mas indisponível. |

## Arquivos publicados

- `index.html`: Central de Módulos, login, consulta de licenças e navegação.
- `cargos-salarios.html`: módulo de Cargos e Salários, mantido como página independente.
- `README.md`: documentação do projeto e registro de mudanças.

**Importante:** manter os nomes `index.html` e `cargos-salarios.html` e os dois arquivos na raiz do repositório, pois a navegação utiliza esses caminhos relativos.

## Arquitetura e serviços

- **GitHub Pages:** hospeda os arquivos HTML da Central e dos módulos.
- **Supabase:** autenticação, sessões e consulta de licenças dos usuários.
- **Hotmart:** processamento das compras; a integração com Supabase é responsável pela liberação de acesso conforme a compra.
- **Electron:** aplicativo Windows que carrega a aplicação web hospedada. Alterações no HTML publicado normalmente não exigem novo instalador, mas podem depender de recarregamento ou expiração do cache.

Endereço da Central: https://rhperformadigital.github.io/rhperforma-app/

## Funcionamento do módulo Cargos e Salários

A Central consulta as licenças no Supabase e apresenta o botão conforme o resultado:

| Resultado da verificação | Indicação | Ação |
| --- | --- | --- |
| Licença com status `active` | `Licença ativa` | `Acessar Cargos e Salários →`, abre `cargos-salarios.html`. |
| Consulta bem-sucedida, sem licença `active` | `Sem licença ativa` | `Conhecer e adquirir →`, abre a página de vendas. |
| Erro ao consultar licenças | `Licença não verificada` | Botão desabilitado; não concede acesso nem oferece compra como se a situação fosse conhecida. |

**Observação:** a Central atual não diferencia, na interface, uma compra nunca realizada de uma licença cancelada ou expirada. Todos esses casos, quando não há licença ativa, usam `Conhecer e adquirir`.

Página de vendas configurada para Cargos e Salários:

https://sites.google.com/view/cargosesalarios-app/cargos-e-salarios-app

O link é definido no `index.html` no objeto JavaScript `SALES_PAGES`. Quando a página de vendas migrar para o site oficial da RH Performa, será suficiente alterar esse endereço e publicar o arquivo atualizado.

## O que mudou em relação à versão anterior da Central (V7)

**Atualização: 21/10/2026 — Central com direcionamento comercial**

1. **Compra para usuários sem licença:** o botão do card Cargos e Salários deixa de ficar apenas bloqueado quando a consulta confirma ausência de licença ativa e passa a mostrar `Conhecer e adquirir →`.
2. **Link de vendas:** o botão passa a abrir a página de vendas do Google Sites em uma nova janela/aba, em vez de encaminhar diretamente ao checkout.
3. **Separação do endereço comercial:** incluído o objeto `SALES_PAGES` para facilitar a futura troca pela página oficial.
4. **Tratamento de falha na verificação:** quando a consulta de licença falha, o card informa `Licença não verificada` e o botão permanece desabilitado.
5. **Acesso de clientes licenciados preservado:** usuários com licença ativa continuam entrando normalmente no módulo Cargos e Salários.
6. **Módulos futuros preservados:** Recrutamento e Seleção e DISC continuam identificados como em desenvolvimento, sem liberação de acesso.

**Não foi necessário alterar** o módulo `cargos-salarios.html` nem recompilar o aplicativo Electron para implementar esse redirecionamento comercial na Central.

## Como publicar atualizações

1. Edite o arquivo correspondente ao componente alterado (`index.html` para a Central ou `cargos-salarios.html` para o módulo).
2. Faça o commit do arquivo atualizado na branch `main` do repositório `RhPerformaDigital/rhperforma-app`.
3. Aguarde a publicação do GitHub Pages.
4. Teste a URL publicada em uma janela anônima e no aplicativo Windows.
5. Confirme os fluxos de login, licença ativa, licença não ativa e acesso aos módulos.

### Atenção ao cache

Foi observado durante os testes que o Chrome pode manter uma versão antiga do HTML em cache, fazendo o módulo retornar à Central mesmo após uma correção. A desativação temporária do cache no DevTools permitiu carregar a versão atualizada. Por isso, validar o conteúdo efetivamente publicado e planejar uma estratégia de atualização/cache para produção.

### Atenção aos dados locais

O módulo de Cargos e Salários utiliza armazenamento local do navegador para parte dos dados. Evite limpar os dados do site ou modificar chaves de armazenamento sem antes exportar um backup e verificar a compatibilidade. O armazenamento no navegador e no Electron pode ser separado.

## Aplicativo Windows

O projeto Electron é mantido separadamente dos arquivos web. Nos testes iniciais, foi possível iniciar o aplicativo, autenticar, verificar a licença, abrir o módulo e gerar/instalar o instalador Windows `RH Performa Setup 1.0.0.exe`.

Para o projeto Electron já configurado:

```bash
npm install
npm start
npm run dist
```

O comando `npm run dist` gera os artefatos na pasta `dist` do projeto Electron. Os clientes finais não precisam instalar Node.js.

**Antes da distribuição comercial:** testar exportação/importação de arquivos, backup de dados, atualização/cache, dependências de segurança e assinatura digital do instalador. A geração do `.exe` por si só não comprova que o aplicativo esteja assinado por um certificado confiável.

## Segurança e licenciamento

- O controle de licença deve continuar sendo verificado pelo Supabase; a interface da Central não substitui a validação de acesso do módulo.
- Nunca colocar chaves privadas ou `service_role` do Supabase em HTML público ou no pacote Electron.
- A disponibilidade do módulo depende de autenticação e licença ativa, conforme as regras implementadas.
- Mudanças no fluxo de compras devem ser testadas também com contas sem licença e com licença ativa.

## Histórico resumido

| Marco | Descrição |
| --- | --- |
| Central V7 | Central modular com login e validação de licença no Supabase. |
| Integração do módulo | Acesso a `cargos-salarios.html` para usuários licenciados. |
| Electron Desktop 1.0.0 | Primeira compilação e instalação local do aplicativo Windows, carregando a Central online. |
| Atualização comercial — 21/10/2026 | Botão `Conhecer e adquirir` para quem não tem licença ativa, com link configurável para a página de vendas. |

---

**RH Performa** — documentação da versão atual da Central. Este README descreve o estado conhecido e testado até 21/10/2026; recursos futuros não devem ser considerados implementados até sua validação.
