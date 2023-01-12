# Введение в распределенные технологии

---

Этот репозиторий содержит отчёты по лабораторным работам курса "Введение в распределенные технологии"

---

## Экзаменационные вопросы

### 6 Что такое ConfigMap и Secrets? - основные понятия, виды ресурсов + манифесты для каждого типа ресурсов

ConfigMap - это объект API, используемый для хранения неконфиденциальных данных в парах ключ-значение. Модули (Pod) могут использовать ConfigMap в качестве переменных среды, аргументов командной строки или в виде файлов конфигурации в volume.
ConfigMap позволяет отделить конфигурацию, зависящую от конкретной среды, от изображений контейнеров, чтобы приложения были легко переносимыми.
Стоит использовать ConfigMap для настройки конфигурационных данных отдельно от кода приложения. Например, это полезно, когда у нас есть несколько БД (тест, прод, дев) и URL к БД берется из переменной окружения (DATABASE_URL).
Стоит учитывать, что ConfigMap не рассчитана на большое кол-во данных (более 1 МиБ).
Содержит поля типа ключ-значение data, binaryData, ключи которых должны содержать "-" | "_" | ".". Ключи data & binaryData не должны пересекаться.

Пример ConfigMap:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"

  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5    
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true  
```

Secrets - это объект, содержащий небольшое количество конфиденциальных данных, таких как пароль, токен или ключ. В противном случае такая информация могла бы быть помещена в спецификацию модуля или в образ контейнера. Использование секрета означает, что нам не нужно включать конфиденциальные данные в код нашего приложения.
Поскольку Secrets могут создаваться независимо от модулей, которые их используют, существует меньший риск раскрытия секрета (и его данных) во время рабочего процесса создания, просмотра и редактирования модулей. Kubernetes и приложения, работающие в нашем кластере, также могут принимать дополнительные меры предосторожности в отношении секретов, например, избегать записи секретных данных в энергонезависимое хранилище.

Secrets похожи на ConfigMap, но специально предназначены для хранения конфиденциальных данных.
Секреты помещаются в .spec.volumes.
Пример Модуля (Pod) с секретом "mysecret"
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
      optional: false # default setting; "mysecret" must exist
```

### 23 Параметры ERC721, ERC1155, примеры программного кода.
ERC-721 - это стандарт невзаимозаменяемого токена (NFT), написанный на языке Solidity в блокчейне Ethereum. Это позволяет разработчикам токенизировать владение любыми произвольными данными. В частности, стандарт направлен на создание взаимозаменяемых токенов. Стандарт ERC-721 был создан Уильямом Энтрикеном, Дитером Ширли, Джейкобом Эвансом и Натассией Сакс в 2018 году.
По сути, каждый токен ERC-721 уникален и представляет собой один актив. Более того, это позволяет разработчикам создавать совершенно новую экосистему токенов на блокчейне Ethereum.

Функции:

1) balanceOf(owner)

2) ownerOf(tokenId)

3) safeTransferFrom(from, to, tokenId)

4) transferFrom(from, to, tokenId)

5) approve(to, tokenId)

6) getApproved(tokenId)

7) setApprovalForAll(operator, _approved)

8) isApprovedForAll(owner, operator)

9) safeTransferFrom(from, to, tokenId, data)

События:

1) Transfer(from, to, tokenId)

2) Approval(owner, approved, tokenId)

3) ApprovalForAll(owner, operator, approved)

Пример кода:
```ERC-721
pragma solidity ^0.4.23;

import "../node_modules/XXX/contracts/token/ERC721/ERC721Token.sol";

contract MyERC721 is ERC721Token {
    constructor (string _name, string _symbol) public
        ERC721Token(_name, _symbol)
    {
    }

    /**
    * Custom accessor to create a unique token
    */
    function mintUniqueTokenTo(
        address _to,
        uint256 _tokenId,
        string  _tokenURI
    ) public
    {
        super._mint(_to, _tokenId);
        super._setTokenURI(_tokenId, _tokenURI);
    }
}
```

ERC-1155 - Улучшенный стандарт после ERC-721, является еще одним стандартом токенов в блокчейне Ethereum, который облегчает создание обоих видов токенов, взаимозаменяемых и невзаимозаменяемых. Цель состоит в том, чтобы создать интерфейс смарт-контракта, который может представлять оба типа. 
Стандарт ERC-1155, в частности, имеет ту же функциональность, что и токен ERC-721. Однако он улучшает функциональность обоих стандартов и в целом является более эффективным стандартом. Что касается преимуществ, транзакции, использующие стандарт ERC-1155, могут быть объединены вместе, чтобы снизить стоимость торговли токенами.

Функции:

1) balanceOf(account, id)

2) balanceOfBatch(accounts, ids)

3) setApprovalForAll(operator, approved)

4) isApprovedForAll(account, operator)

5) safeTransferFrom(from, to, id, amount, data)

6) safeBatchTransferFrom(from, to, ids, amounts, data)

События:

1) TransferSingle(operator, from, to, id, value)

2) TransferBatch(operator, from, to, ids, values)

3) ApprovalForAll(account, operator, approved)

4) URI(value, id)

Пример кода:
```ERC-1155
pragma solidity ^0.5.0;

import "./SafeMath.sol";
import "./Address.sol";
import "./Common.sol";
import "./IERC1155TokenReceiver.sol";
import "./IERC1155.sol";

contract ERC1155 is IERC1155, ERC165, CommonConstants
{
    using SafeMath for uint256;
    using Address for address;

    mapping (uint256 => mapping(address => uint256)) internal balances;

    mapping (address => mapping(address => bool)) internal operatorApproval;

    bytes4 constant private INTERFACE_SIGNATURE_ERC165 = 0x01ffc9a7;

    bytes4 constant private INTERFACE_SIGNATURE_ERC1155 = 0xd9b67a26;

    function supportsInterface(bytes4 _interfaceId)
    public
    view
    returns (bool) {
         if (_interfaceId == INTERFACE_SIGNATURE_ERC165 ||
             _interfaceId == INTERFACE_SIGNATURE_ERC1155) {
            return true;
         }

         return false;
    }

    function safeTransferFrom(address _from, address _to, uint256 _id, uint256 _value, bytes calldata _data) external {

        require(_to != address(0x0), "_to must be non-zero.");
        require(_from == msg.sender || operatorApproval[_from][msg.sender] == true, "Need operator approval for 3rd party transfers.");

        balances[_id][_from] = balances[_id][_from].sub(_value);
        balances[_id][_to]   = _value.add(balances[_id][_to]);

        emit TransferSingle(msg.sender, _from, _to, _id, _value);

        if (_to.isContract()) {
            _doSafeTransferAcceptanceCheck(msg.sender, _from, _to, _id, _value, _data);
        }
    }

    function safeBatchTransferFrom(address _from, address _to, uint256[] calldata _ids, uint256[] calldata _values, bytes calldata _data) external {

        for (uint256 i = 0; i < _ids.length; ++i) {
            uint256 id = _ids[i];
            uint256 value = _values[i];

            // SafeMath will throw with insuficient funds _from
            // or if _id is not valid (balance will be 0)
            balances[id][_from] = balances[id][_from].sub(value);
            balances[id][_to]   = value.add(balances[id][_to]);
        }

        emit TransferBatch(msg.sender, _from, _to, _ids, _values);

        if (_to.isContract()) {
            _doSafeBatchTransferAcceptanceCheck(msg.sender, _from, _to, _ids, _values, _data);
        }
    }

    function balanceOf(address _owner, uint256 _id) external view returns (uint256) {
        // The balance of any account can be calculated from the Transfer events history.
        // However, since we need to keep the balances to validate transfer request,
        // there is no extra cost to also privide a querry function.
        return balances[_id][_owner];
    }

    function balanceOfBatch(address[] calldata _owners, uint256[] calldata _ids) external view returns (uint256[] memory) {

        require(_owners.length == _ids.length);

        uint256[] memory balances_ = new uint256[](_owners.length);

        for (uint256 i = 0; i < _owners.length; ++i) {
            balances_[i] = balances[_ids[i]][_owners[i]];
        }

        return balances_;
    }

    function setApprovalForAll(address _operator, bool _approved) external {
        operatorApproval[msg.sender][_operator] = _approved;
        emit ApprovalForAll(msg.sender, _operator, _approved);
    }

    function isApprovedForAll(address _owner, address _operator) external view returns (bool) {
        return operatorApproval[_owner][_operator];
    }

    function _doSafeTransferAcceptanceCheck(address _operator, address _from, address _to, uint256 _id, uint256 _value, bytes memory _data) internal {

        require(ERC1155TokenReceiver(_to).onERC1155Received(_operator, _from, _id, _value, _data) == ERC1155_ACCEPTED, "contract returned an unknown value from onERC1155Received");
    }

    function _doSafeBatchTransferAcceptanceCheck(address _operator, address _from, address _to, uint256[] memory _ids, uint256[] memory _values, bytes memory _data) internal {
        require(ERC1155TokenReceiver(_to).onERC1155BatchReceived(_operator, _from, _ids, _values, _data) == ERC1155_BATCH_ACCEPTED, "contract returned an unknown value from onERC1155BatchReceived");
    }
}
```

Отличия:

1) Смарт-контракт
Cтандарт ERC-721 производит исключительно NFT и заставляет разработчиков создавать смарт-контракт для каждого нового токена. С другой стороны, ERC-1155 позволяет разработчикам разрабатывать единый смарт-контракт, который можно использовать для минтинга взаимозаменяемых токенов или NFT.

2) Эффективность
Поскольку ERC-721 допускает одну операцию для каждой транзакции, это дорого и требует много времени. В то же время это снижает эффективность сети блокчейна с избыточным кодом. Тогда как ERC-1155 позволяет выполнять несколько операций в одной транзакции. Поэтому транзакции дешевле и эффективнее. Кроме того, в отличие от ERC-721, который использует значительное пространство, ERC-1155 использует меньше места для хранения в сети блокчейна.
