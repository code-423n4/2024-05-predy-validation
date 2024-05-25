# 概要
Transient Storageを利用する

# 問題点
現状、ReentrancyGuardUpgradeableを利用して、リエントランシ~攻撃を防いでいる

# 問題である理由
ストレージにフラグを書き込んでいるため、ガスが余計にかかる。

# 具体的な対応例

トランザクション実行中、一時的に利用できるTransient Storageの機能を使って対応することにより、今よりガスコストを抑えることができる

参考
https://soliditylang.org/blog/2024/01/26/transient-storage/

# 補足
Solidity 0.8.24からの機能になるので、利用する場合はネットワークの対応状況を確認し、必要に応じてデフォルトEVMの変更をする必要があると思います。