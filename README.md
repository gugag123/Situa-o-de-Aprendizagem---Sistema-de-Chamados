Sistema de Gestão de Chamados – Equipe 3
Help desk com fluxo N1 → N2 → N3, matriz de prioridade, SLA, histórico de tramitações, base de conhecimento, suporte remoto e dashboard. Spring Boot 3 · Spring Data JPA · Spring Security · Thymeleaf + Bootstrap 5 · MySQL.

📄 Documento técnico (GLPI x sistema próprio, fluxograma, tabela dos 10 chamados, respostas): docs/DOCUMENTACAO.md

Requisitos
Java 17+
Maven 3.8+ (ou a IDE: IntelliJ / Eclipse / VS Code)
MySQL 8 rodando em localhost:3306
Como executar
# 1) Ajuste usuário/senha do MySQL (padrão: root / root) via variáveis de ambiente, se necessário
export DB_USER=root
export DB_PASSWORD=root

# 2) Suba a aplicação
mvn spring-boot:run
Acesse http://localhost:8080 – senha de todos os usuários: 123456.

Para testar como…	Login
Supervisor (dashboard, todos os níveis)	supervisor@softhouse.com
Técnico N1	ana.n1@softhouse.com / bruno.n1@softhouse.com
Técnico N2	carla.n2@softhouse.com / gustavo.n2@softhouse.com
Técnico N3 (desenvolvimento)	daniel.n3@softhouse.com / eduardo.n3@softhouse.com
Solicitante	joao@alfavarejo.com.br, pedro@betalogistica.com.br, patricia@gamasaude.com.br, lucas@gamasaude.com.br…
Banco de dados
O banco chamados_db é criado automaticamente (createDatabaseIfNotExist=true).
Na subida o Spring executa src/main/resources/schema.sql (DDL) e data.sql (seed com 15 usuários, 3 grupos, 8 categorias, SLA, 6 artigos e os 10 chamados de teste com histórico).
Atenção: com DB_INIT=always (padrão) o banco é recriado a cada inicialização – ideal para demonstração. Para manter o que você cadastrou, suba com DB_INIT=never depois da primeira execução.
Para rodar os scripts manualmente: mysql -u root -p chamados_db < sql/01_schema.sql e depois sql/02_data.sql (cópias dos arquivos de src/main/resources).
Gerar o .jar
mvn clean package
java -jar target/sistema-chamados-1.0.0.jar
Roteiro rápido de demonstração
Solicitante joao@alfavarejo.com.br → Abrir chamado → ao escolher tipo, impacto e urgência aparece o resultado do cálculo (pontos, prioridade, SLA e nível responsável). Ex.: Bug Crítico x Crítica = 8 pontos → N2.
Técnico N1 ana.n1@… → Minha fila → assumir → Escalonar para N2 com justificativa.
Técnico N2 carla.n2@… → assumir → escalar para N3. (O N2 e o N3 também podem assumir direto chamados de níveis abaixo: em Minha fila use as abas N1/N2.)
Técnico N3 daniel.n3@… → assumir → registrar solução (o campo Release/Commit/Hotfix é obrigatório).
Solicitante → aprova ou reprova a solução → chamado Fechado. Todo o caminho aparece no histórico.
Suporte remoto: técnico Solicitar acesso → o solicitante vê um aviso no topo do chamado e clica em Aceitar ou Recusar → se aceitar, o técnico registra o diagnóstico.
Supervisor → Dashboard. Chamados de referência: #9 (N1→N2→N3), #6 (crítico com hotfix), #8 (remoto).
Saída automática por inatividade
Depois de 20 segundos sem mexer no site (mouse, teclado, rolagem ou toque), o usuário sai da conta automaticamente. O contador aparece na barra superior (fica vermelho nos últimos 5 s). Para mudar o tempo, altere app.inatividade.segundos no application.properties.

Estrutura
src/main/java/br/com/equipe3/chamados
├── model/        entidades JPA e enums
├── repository/   Spring Data JPA
├── service/      regras: prioridade/SLA, fluxo, escalonamento automático, dashboard
├── web/          controllers + formulários
└── config/       Spring Security
src/main/resources
├── templates/    telas Thymeleaf
├── schema.sql    DDL MySQL
└── data.sql      carga inicial (seed)
docs/             documento técnico + fluxograma
