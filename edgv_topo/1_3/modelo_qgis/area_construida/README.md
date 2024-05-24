# EDGV 3.0 Topo 1.3: Models QGIS Área Construida

------------------------------------

Modelos construídos para a produção EDGV 3.0 Topo versão 1.3, na linha de produção de Conjunto de Dados Geoespaciais Vetoriais.

## Classes utilizadas

- moldura;
- centroide_area_construida_p;
- centroide_elemento_hidrografico_p;
- centroide_ilha_p;
- centroide_massa_dagua_p;
- delimitador_area_construida_l;
- delimitador_elemento_hidrografico_l;
- delimitador_massa_dagua_l;
- elemnat_curva_nivel_l;
- elemnat_elemento_hidrografico_l;
- elemnat_elemento_hidrografico_p;
- elemnat_ilha_p;
- elemnat_sumidouro_vertedouro_p;
- elemnat_trecho_drenagem_l;
- infra_barragem_a;
- infra_barragem_l;
- infra_ferrovia_l;
- infra_mobilidade_urbana_l;
- infra_travessia_hidroviaria_l;
- infra_vala_l;
- infra_via_deslocamento_l;


### Expressão para capturar todas as geometrias carregadas

```
array_to_string ( array_foreach ( array_filter ( array_filter (@layers,not (regexp_match (layer_property (@element,'name'), '(rascunho|rev_|val_|aux_|moldura|Flags|flags)'))), layer_property (@element,'geometry_type') in ('Polygon','Line', 'Point')), layer_property (@element,'name')))
```

## Ordem dos processos

1. Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
2. Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos / Identificar ângulos fora de Limites;
3. Identificar Geometrias duplicadas / Identificar Overlaps / Identificar Geometrias inválidas (com correção automática);
4. Unir linhas de mesmo conjunto de atributos;
5. Identificar linhas entrelaçadas;
6. Limpeza topológica (1e-6) / Remover elementos pequenos (1e-5 - 1m);
7. Identificar Geometrias duplicadas / Identificar Overlaps / Identificar Geometrias inválidas (com correção automática);
8. Ajustar conectividade das linhas (1m de raio) / Adicionar vértices não compartilhados nas intersecções / Adicionar vértices não compartilhados em segmentos compartilhados / Unir linhas / Desagregar geometrias;
9. Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
10. Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos / Identificar ângulos pequenos entre camadas;
11. Snap Hierárquico;
12. Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
13. Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos / Identificar ângulos pequenos entre camadas;
14. Limpeza topológica (1e-5) / Remover elementos pequenos (1m) / Ajustar conectividade das linhas (1m de raio) / Remover feições duplicadas;
15. Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
16. Identificar Geometrias duplicadas / Identificar Overlaps / Identificar Geometrias inválidas (com correção automática);
17. Unir linhas de mesmo conjunto de atributos;
18. Suavização de Douglas-Peucker / Unir linhas;
19. Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
20. Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos / Identificar ângulos fora de Limites;
21. 
14. Identificar Geometrias inválidas (com correção automática) / Identificar vértices próximos de arestas / Identificar vérfice não compartilhado nas intersecções / Identificar vértice não compartilhado em segmentos compartilhados;
15. Identificar geometrias com densidade incorreta de vértices;
16. Identificar undershoot com moldura e conexão de linhas;
17. Identificar Z;
18. Identificar overlaps;
19. Identificar linhas segmentadas com mesmo conjunto de atributos;
20. Identificar linhas não segmentadas nas intersecções;
21. Identificar pontas soltas pequenas nas linhas;
22. Identificar erros na construção das redes de rodoviárias e ferroviárias;
23. Linha para multilinha;
24. Identificar erros de ortografia nos atributos;
25. Identificar erros de atributação;
26. Identificar erros de relacionamentos espaciais;

## Detalhamento dos processos

### 1. Manipulação preliminar de geometrias

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/manipulacao_preliminar_geometria.model3
- camadas: todas as camadas carregadas;
- processos utilizados: Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- nome camadas flags: flags_unicode_invalido_ponto,flags_unicode_invalido_linha,flags_unicode_invalido_poligono
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.

### 2. Identifica geometrias inválidas (com correção) e ângulos pequenos

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_e_corrige_geometria_invalida_identifica_angulos_pequenos.model3
- processos utilizados: Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos (10 graus) / Identificar angulos fora de limites na cobertura;
- camadas: todas as camadas carregadas;
- nome camada flags: flags_geometrias_invalidas
- admite falsos positivos? Não.
- para após a execução? Somente se tiver flags.
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 3. Identifica problemas de construção entre geometrias  

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_problemas_construcao_entre_geometrias.model3
- processos utilizados: Identificar Geometrias duplicadas / Identificar overlaps / Identificar Geometrias inválidas (com correção automática)
- obs: fluxo genérico para atender diversas etapas de produção (atende os casos de ponto, linha e polígono)
- camada: todas as camadas;
- nome camada flags: flags_p, flags_l, flags_a
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 4. Unir linhas com mesmo conjunto de atributos

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/unir_linhas_com_mesmo_conjunto_de_atributos.model3
- processos utilizados: Unir linhas com mesmo conjunto de atributos
- camada: todas as camadas do tipo linha carregadas;
- nome camada flags: não aponta flags;
- admite falsos positivos? Não é o caso;
- para após a execução? Não.
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- Texto para tooltip: O algoritmo une linhas com mesmo conjunto de atributos.

### 5. Identificar linhas entrelaçadas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/area_construida/identifica_area_construida_entrelacados.model3
- processos utilizados: Identify Intertwined Lines;
- camada: delimitador_area_construida_l;
- nome camada flags: flags_linhas_entrelacadas;
- admite falsos positivos? Não;
- para após a execução? Somente se tiver flags;
- Texto para tooltip: O operador deve corrigir manualmente linhas que se entrelaçam. Normalmente, tais problemas são de digitalização.
  
### 6. Limpeza Suave das Linhas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/limpeza_suave_linhas.model3
- processos utilizados: Clean geometries (1e-6) / Remove small lines (1e-5) / Remove Duplicated Features;
- camada: todas as linhas;
- nome camada flags: não há;
- admite falsos positivos? Não é o caso;
- nome da camada de saída: saida_clean_flags
- para após a execução? Sim
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.
  
### 7. Identifica problemas de construção entre geometrias  

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_problemas_construcao_entre_geometrias.model3
- processos utilizados: Identificar Geometrias duplicadas / Identificar overlaps / Identificar Geometrias inválidas (com correção automática)
- obs: fluxo genérico para atender diversas etapas de produção (atende os casos de ponto, linha e polígono)
- camada: todas as camadas;
- nome camada flags: flags_p, flags_l, flags_a
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.
  
### 8. Corrige compartilhamento de vértices entre camadas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/corrige_compartilhamento_de_vertices.model3
- processos utilizados: Ajustar conectividade das linhas (1m de raio) / Adicionar vértices não compartilhados nas intersecções / Adicionar vértices não compartilhados em segmentos compartilhados / Unir linhas / Desagregar geometrias
- obs: fluxo genérico para atender diversas etapas de produção (atende os casos de ponto, linha e polígono)
- camada: todas as camadas;
- nome camada flags: não é o caso
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.

### 9. Manipulação preliminar de geometrias

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/manipulacao_preliminar_geometria.model3
- camadas: todas as camadas carregadas;
- processos utilizados: Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- nome camadas flags: flags_unicode_invalido_ponto,flags_unicode_invalido_linha,flags_unicode_invalido_poligono
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.

### 10. Identificar geometrias inválidas e ângulos pequenos entre camadas pós correção de vértices 

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_e_corrige_geometria_invalida_identifica_angulos_pequenos.model3
- processos utilizados: Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos (10 graus) / Identificar ângulos pequenos entre camadas;
- camadas: todas as camadas carregadas;
- nome camada flags: flags_geometrias_invalidas
- admite falsos positivos? Não.
- para após a execução? Somente se tiver flags.
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 11. Snap Hierárquico

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/area_construida/snap_hierarquico_area_edificada.model3
- processos utilizados: Snap Hierárquico
- configuração do snap hierárquico: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/area_construida/snap_hierarquico_area_edificada.json

### 12. Manipulação preliminar de geometrias pós snap

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/manipulacao_preliminar_geometria.model3
- camadas: todas as camadas carregadas;
- processos utilizados: Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- nome camadas flags: flags_unicode_invalido_ponto,flags_unicode_invalido_linha,flags_unicode_invalido_poligono
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.

### 13. Identificar geometrias inválidas e ângulos pequenos entre camadas pós snap

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_e_corrige_geometria_invalida_identifica_angulos_pequenos.model3
- processos utilizados: Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos (10 graus) / Identificar ângulos pequenos entre camadas;
- camadas: todas as camadas carregadas;
- nome camada flags: flags_geometrias_invalidas
- admite falsos positivos? Não.
- para após a execução? Somente se tiver flags.
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 14. Limpeza completa das linhas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/limpeza_completa_linhas.model3
- processos utilizados: Limpeza topológica (1e-5) / Remover elementos pequenos (1m) / Ajustar conectividade das linhas (1m de raio) / Remover feições duplicadas;
- camada: todas as linhas;
- nome camada flags: não há;
- admite falsos positivos? Não é o caso;
- nome da camada de saída: saida_clean
- para após a execução? Sim
- Texto para tooltip: 
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- para após a execução? Sim
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 15. Manipulação preliminar de geometrias pós limpeza completa

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/manipulacao_preliminar_geometria.model3
- camadas: todas as camadas carregadas;
- processos utilizados: Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- nome camadas flags: flags_unicode_invalido_ponto,flags_unicode_invalido_linha,flags_unicode_invalido_poligono
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.

### 16: Identifica problemas de construção entre geometrias pós limpeza completa

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_problemas_construcao_entre_geometrias.model3
- processos utilizados: Identificar Geometrias duplicadas / Identificar overlaps / Identificar Geometrias inválidas (com correção automática)
- obs: fluxo genérico para atender diversas etapas de produção (atende os casos de ponto, linha e polígono)
- camada: todas as camadas;
- nome camada flags: flags_p, flags_l, flags_a
- Texto para tooltip: O operador deve salvar manualmente todas as camadas.


### 17. Unir linhas com mesmo conjunto de atributos

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/unir_linhas_com_mesmo_conjunto_de_atributos.model3
- processos utilizados: Unir linhas com mesmo conjunto de atributos
- camada: todas as camadas do tipo linha carregadas;
- nome camada flags: não aponta flags;
- admite falsos positivos? Não é o caso;
- para após a execução? Não.
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- Texto para tooltip: O algoritmo une linhas com mesmo conjunto de atributos.

### 18. Simplificação de Douglas-Peucker

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/simplificacao_linhas.model3
- processos utilizados: Topological Douglas/Unir linhas;
- camada: todas as linhas;
- nome camada flags: não há;
- admite falsos positivos? Não é o caso;
- nome da camada de saída: flags_suavizacao
- para após a execução? Sim
- Texto para tooltip: 
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 19. Manipulação preliminar de geometrias pós Simplificação 

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/manipulacao_preliminar_geometria.model3
- camadas: todas as camadas carregadas;
- processos utilizados: Remover geometrias nulas / Desagregar geometrias / Remover vértices duplicados / Remover feições duplicadas / identify features with invalid unicode;
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf","geometry_error","observacao","operador_criacao","data_criacao","operador_atualizacao","data_atualizacao"]
- nome camadas flags: flags_unicode_invalido_ponto,flags_unicode_invalido_linha,flags_unicode_invalido_poligono

### 20. Identifica geometrias inválidas (com correção) e ângulos pequenos pós Simplificação 

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_e_corrige_geometria_invalida_identifica_angulos_pequenos.model3
- processos utilizados: Identificar Geometrias inválidas (com correção automática) / Identificar ângulos pequenos (10 graus);
- camadas: todas as camadas carregadas;
- nome camada flags: flags_geometrias_invalidas
- admite falsos positivos? Não.
- para após a execução? Somente se tiver flags.
- Texto para tooltip: O operador deve corrigir manualmente os apontamentos desse processo.

### 14: Identifica problemas de compartilhamento de vértices   

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_problemas_compartilhamento_vertices.model3
- processos utilizados: Identificar Geometrias inválidas (com correção automática) / Identificar vértices próximos de arestas / Identificar vérfice não compartilhado nas intersecções / Identificar vértice não compartilhado em segmentos compartilhados;
- nome camada flags: flag_geometrias_invalidas,flag_vertices_proximo_arestas,flag_vertices_nao_compartilhados_interseccoes,flag_vertice_nao_compartilhado_em_seg_compartilhado, flag_linha_nao_seccionada_na_interseccao
- Texto para tooltip: Todas as feições devem compartilhar vértices, logo, onde for apontado erro, deve-se adicionar o vértice nas linhas que possuem intersecção ponto ou linha.

### 15: Identificar geometrias com densidade incorreta de vértices

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_geometrias_com_densidade_incorreta_de_vertices.model3
- camadas: todas as camadas carregadas;
- tol: 0.00001 grau
- nome camada flags: flag_densidade_incorreta_vertices

### 16. Identificar undershoot com moldura e conexão de linhas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_undershoot_moldura_conexao_linhas.model3
- camadas linha: todas do tipo linha
- camadas poligono: nenhuma
- camada de moldura: aux_moldura_area_continua_a | aux_moldura_a | moldura
- nome camada flags: flags_undershoot
- pode admitir falso positivo? sim

### 17. Identificar Z

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_z.model3
- camadas: todas carregadas
- nome camada flags: flag_z


### 18. Identificar overlaps dentro da mesma camada

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identifica_overlaps_linhas.model3
- camadas: todas
- nome camada flags: flags_overlaps_l


### 19. Identificar linhas segmentadas com mesmo conjunto de atributos * NÃO EXISTE *

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/via_deslocamento/identifica_linhas_segmentadas_com_mesmo_conjunto_de_atributos_transportes.model3
- camadas: infra_ferrovia_l,infra_mobilidade_urbana_l,infra_travessia_hidroviaria_l,infra_via_deslocamento_l
- camada de moldura: aux_moldura_area_continua_a | aux_moldura_a | moldura
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf", "geometry_error", "observacao", "operador_criacao", "data_criacao", "operador_atualizacao", "data_atualizacao"]
- nome camada flags: flags_linhas_nao_unidas
n

### 19. Seccionar linhas com linhas


### 20. Identificar pontas soltas de delimitadores de area edificada e linhas não segmentadas

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis\area_construida\identificar_pontas_soltas_delimitadores_area_edificada.model3
- camadas: infra_ferrovia_l,infra_via_deslocamento_l,delimitador_area_construida_l,elemnat_trecho_drenagem_l,infra_barragem_l,infra_vala_l,infra_mobilidade_urbana_l
- nome camada flags: flags_elem_rede_nao_segmentados


### 21. Identificar pontas soltas pequenas (FALSO POSITIVO)

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identificar_pontas_soltas_pequenas_falso_positivo.model3
- camadas do pontas livre de primeira ordem: Todas Camadas
- raio de busca: 1000 m (0.01 grau)
- tamanho: 5 m (0.00005 grau)
- nome camada flags: flags_pontas_soltas_pequenas

### 22. Identificar linhas não mergeadas com mesmo atributo

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/gerais/identificar_linhas_nao_mergeadas_com_mesmo_atributo.model3
- camadas: Todas Camadas
- camadas filtro linha: infra_ferrovia_l,infra_mobilidade_urbana_l,infra_travessia_hidroviaria_l
- black list de atributos: ["id","texto_edicao","label_x","label_y","justificativa_txt","tamanho_txt","visivel","carta_simbolizacao","simbolizar_carta_mini","simb_rot","rotular_carta_mini","espacamento","tamanho_txt","estilo_fonte","cor","cor_buffer","tamanho_buffer","observacao","length_otf", "geometry_error", "observacao", "operador_criacao", "data_criacao", "operador_atualizacao", "data_atualizacao"]
- nome camada flags: flags_redes_transporte

### 23. Linha para multilinha de delimitador de área construida    

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/via_deslocamento/linha_para_multilinha_rodovia.model3;
- camadas: infra_via_deslocamento_l
- nome saida: multilinha
- para após a execução? Sim

### 25. Identificar erro no atributo do centroide de area construida

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/area_construida/Identifica_erro_ no_atributo_do_centroide_de_area_construida.model3
- camadas: todas;
- para após a execução? Sim
- nome camada de flags: flags_erros_atributos
- nome camada de saída: atributos_incomuns

### 24. Identifica erro ortografia atributo nome do centroide de area construida  

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/area_construida/Identifica_erro_ no_atributo_do_centroide_de_area_construida.model3;
- camadas: todas;
- para após a execução? Sim
- nome camada de saída: saida_verifica_ortografia_nome



### 26. Identificar erros de relacionamentos espaciais   *VERIFICAR*

- arquivo: /configuracoes_producao/edgv_topo/1_3/modelo_qgis/via_deslocamento/identifica_erros_relacionamentos_espaciais_transportes.model3
- camadas: todas;
- nome camada de flags: flags_ponto,flags_linha