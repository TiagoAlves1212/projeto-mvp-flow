# Estrutura do Banco de Dados - Cardápio Digital

Este documento descreve a estrutura completa do banco de dados do sistema de cardápio digital para lanchonetes.

## Tabelas Principais

### 1. `stores` (Lojas)
Armazena informações das lojas cadastradas no sistema.

**Campos:**
- `id` (UUID): Identificador único
- `name` (VARCHAR): Nome da loja
- `logo_url` (TEXT): URL do logo da loja
- `slug` (VARCHAR): Slug único para o link exclusivo do cardápio
- `whatsapp` (VARCHAR): WhatsApp da loja
- `email` (VARCHAR): Email da loja
- `phone` (VARCHAR): Telefone da loja
- `address` (TEXT): Endereço da loja
- `is_active` (BOOLEAN): Se a loja está ativa
- `created_at`, `updated_at`: Timestamps

### 2. `store_admins` (Administradores das Lojas)
Relaciona usuários do sistema de autenticação com lojas.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `user_id` (UUID): Referência ao usuário (auth.users)
- `role` (VARCHAR): Papel do admin (admin, owner, etc)
- `created_at`: Timestamp

### 3. `customers` (Clientes)
Armazena informações dos clientes.

**Campos:**
- `id` (UUID): Identificador único
- `user_id` (UUID): Referência ao usuário (auth.users)
- `name` (VARCHAR): Nome do cliente
- `cpf` (VARCHAR): CPF do cliente
- `whatsapp` (VARCHAR): WhatsApp do cliente
- `email` (VARCHAR): Email do cliente
- `created_at`, `updated_at`: Timestamps

### 4. `customer_addresses` (Endereços dos Clientes)
Armazena endereços de entrega dos clientes.

**Campos:**
- `id` (UUID): Identificador único
- `customer_id` (UUID): Referência ao cliente
- `label` (VARCHAR): Rótulo do endereço (Casa, Trabalho, etc)
- `street` (VARCHAR): Rua
- `number` (VARCHAR): Número
- `complement` (VARCHAR): Complemento
- `neighborhood` (VARCHAR): Bairro
- `city` (VARCHAR): Cidade
- `state` (VARCHAR): Estado (2 caracteres)
- `zip_code` (VARCHAR): CEP
- `latitude` (DECIMAL): Latitude
- `longitude` (DECIMAL): Longitude
- `is_default` (BOOLEAN): Se é o endereço padrão
- `created_at`, `updated_at`: Timestamps

### 5. `delivery_zones` (Zonas de Entrega)
Define zonas de entrega com preços específicos por raio em km.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `name` (VARCHAR): Nome da zona
- `radius_km` (DECIMAL): Raio em quilômetros
- `delivery_fee` (DECIMAL): Taxa de entrega
- `min_order_value` (DECIMAL): Valor mínimo do pedido
- `is_active` (BOOLEAN): Se a zona está ativa
- `created_at`, `updated_at`: Timestamps

### 6. `menu_categories` (Categorias do Cardápio)
Categorias para organizar os itens do cardápio.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `name` (VARCHAR): Nome da categoria
- `description` (TEXT): Descrição da categoria
- `image_url` (TEXT): URL da imagem da categoria
- `display_order` (INTEGER): Ordem de exibição
- `is_active` (BOOLEAN): Se a categoria está ativa
- `created_at`, `updated_at`: Timestamps

### 7. `menu_items` (Itens do Cardápio)
Itens disponíveis no cardápio.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `category_id` (UUID): Referência à categoria
- `name` (VARCHAR): Nome do item
- `description` (TEXT): Descrição do item
- `image_url` (TEXT): URL da imagem do item
- `price` (DECIMAL): Preço do item
- `is_available` (BOOLEAN): Se o item está disponível
- `display_order` (INTEGER): Ordem de exibição
- `created_at`, `updated_at`: Timestamps

### 8. `menu_item_variations` (Variações dos Itens)
Variações dos itens (tamanhos, adicionais, modificações).

**Campos:**
- `id` (UUID): Identificador único
- `menu_item_id` (UUID): Referência ao item do cardápio
- `name` (VARCHAR): Nome da variação (ex: "Pequeno", "Bacon")
- `type` (VARCHAR): Tipo (size, addon, modification)
- `price_modifier` (DECIMAL): Modificador de preço (+ ou -)
- `is_required` (BOOLEAN): Se é obrigatório
- `display_order` (INTEGER): Ordem de exibição
- `created_at`: Timestamp

### 9. `carts` (Carrinhos)
Carrinhos de compras dos clientes.

**Campos:**
- `id` (UUID): Identificador único
- `customer_id` (UUID): Referência ao cliente
- `store_id` (UUID): Referência à loja
- `created_at`, `updated_at`: Timestamps

**Constraint:** UNIQUE(customer_id, store_id) - Um cliente tem um carrinho por loja

### 10. `cart_items` (Itens do Carrinho)
Itens adicionados ao carrinho.

**Campos:**
- `id` (UUID): Identificador único
- `cart_id` (UUID): Referência ao carrinho
- `menu_item_id` (UUID): Referência ao item do cardápio
- `quantity` (INTEGER): Quantidade
- `unit_price` (DECIMAL): Preço unitário no momento da adição
- `notes` (TEXT): Observações do cliente
- `created_at`, `updated_at`: Timestamps

### 11. `cart_item_modifications` (Modificações dos Itens no Carrinho)
Modificações aplicadas aos itens no carrinho.

**Campos:**
- `id` (UUID): Identificador único
- `cart_item_id` (UUID): Referência ao item do carrinho
- `variation_id` (UUID): Referência à variação
- `created_at`: Timestamp

### 12. `orders` (Pedidos)
Pedidos realizados pelos clientes.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `customer_id` (UUID): Referência ao cliente
- `customer_address_id` (UUID): Referência ao endereço de entrega
- `delivery_zone_id` (UUID): Referência à zona de entrega
- `order_number` (VARCHAR): Número único do pedido (gerado automaticamente)
- `status` (VARCHAR): Status do pedido (pending, confirmed, preparing, out_for_delivery, delivered, cancelled)
- `payment_status` (VARCHAR): Status do pagamento (pending, paid, refunded)
- `payment_method` (VARCHAR): Método de pagamento
- `subtotal` (DECIMAL): Subtotal do pedido
- `delivery_fee` (DECIMAL): Taxa de entrega
- `total` (DECIMAL): Total do pedido
- `notes` (TEXT): Observações do pedido
- `estimated_delivery_time` (INTEGER): Tempo estimado em minutos
- `delivered_at` (TIMESTAMP): Data/hora da entrega
- `created_at`, `updated_at`: Timestamps

### 13. `order_items` (Itens do Pedido)
Itens de um pedido.

**Campos:**
- `id` (UUID): Identificador único
- `order_id` (UUID): Referência ao pedido
- `menu_item_id` (UUID): Referência ao item do cardápio
- `name` (VARCHAR): Nome do item no momento do pedido
- `quantity` (INTEGER): Quantidade
- `unit_price` (DECIMAL): Preço unitário
- `total_price` (DECIMAL): Preço total
- `notes` (TEXT): Observações
- `created_at`: Timestamp

### 14. `order_item_modifications` (Modificações dos Itens do Pedido)
Modificações aplicadas aos itens do pedido.

**Campos:**
- `id` (UUID): Identificador único
- `order_item_id` (UUID): Referência ao item do pedido
- `variation_id` (UUID): Referência à variação
- `variation_name` (VARCHAR): Nome da variação no momento do pedido
- `price_modifier` (DECIMAL): Modificador de preço
- `created_at`: Timestamp

### 15. `messages` (Mensagens)
Mensagens do admin para clientes.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja
- `customer_id` (UUID): Referência ao cliente (NULL para mensagens em massa)
- `subject` (VARCHAR): Assunto da mensagem
- `content` (TEXT): Conteúdo da mensagem
- `message_type` (VARCHAR): Tipo (notification, whatsapp, email)
- `filters` (JSONB): Filtros aplicados
- `sent_at` (TIMESTAMP): Data/hora de envio
- `created_at`: Timestamp
- `created_by` (UUID): Referência ao usuário que criou

### 16. `store_settings` (Configurações da Loja)
Configurações gerais da loja.

**Campos:**
- `id` (UUID): Identificador único
- `store_id` (UUID): Referência à loja (UNIQUE)
- `opening_hours` (JSONB): Horários de funcionamento
- `is_open` (BOOLEAN): Se a loja está aberta
- `min_order_value` (DECIMAL): Valor mínimo do pedido
- `estimated_preparation_time` (INTEGER): Tempo médio de preparo em minutos
- `created_at`, `updated_at`: Timestamps

## Funções Auxiliares

### `calculate_distance(lat1, lon1, lat2, lon2)`
Calcula a distância em km entre duas coordenadas usando a fórmula de Haversine.

### `find_delivery_zone(store_id, latitude, longitude)`
Encontra a zona de entrega válida para um endereço.

### `validate_delivery_address(store_id, latitude, longitude)`
Valida se um endereço está dentro de uma zona de entrega.

### `calculate_cart_total(cart_id)`
Calcula o total de um carrinho.

### `get_store_sales_kpis(store_id, start_date, end_date)`
Retorna KPIs de vendas da loja:
- Total de pedidos
- Receita total
- Valor médio do pedido
- Total de taxas de entrega
- Pedidos por status

### `get_customer_kpis(store_id, customer_id)`
Retorna KPIs de um cliente:
- Total de pedidos
- Total gasto
- Valor médio do pedido
- Data do último pedido
- Itens favoritos

## Row Level Security (RLS)

Todas as tabelas têm RLS habilitado com políticas que garantem:

1. **Clientes** podem ver e editar apenas seus próprios dados
2. **Admins** podem gerenciar dados de suas lojas
3. **Público** pode ver apenas dados públicos (lojas ativas, cardápio disponível)
4. **Isolamento** entre lojas - admins só veem dados de suas próprias lojas

## Triggers

- **updated_at**: Atualiza automaticamente o campo `updated_at` em todas as tabelas relevantes
- **generate_order_number**: Gera automaticamente o número do pedido no formato `PED-YYYYMMDD-XXXXXX`

## Índices

Índices foram criados em todas as chaves estrangeiras e campos frequentemente consultados para otimizar as queries.

