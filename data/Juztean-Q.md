    function setUp() public override {
        TestPool.setUp();

        pairId = registerPair(address(currency1), address(0), false);

        predyPool.supply(pairId, true, 1e8);
        predyPool.supply(pairId, false, 1e8);

        _attackCountract = new AttackCallbackContract(predyPool, address(currency1));
    }