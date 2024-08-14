
我希望像sql数据插入下面的数据，表结构如下
comments对应每条数据下面的#注释说明，
请你编写sql插入数据  
CREATE TABLE cyclic_values ( id TEXT PRIMARY KEY, replace_values TEXT, comments TEXT );

```sql
CREATE TABLE id_values (
    id TEXT PRIMARY KEY,
    replace_values TEXT,
    comments TEXT
);
CREATE TABLE cyclic_values (
    id TEXT PRIMARY KEY,
    replace_values TEXT,
    comments TEXT
);

INSERT OR IGNORE INTO cyclic_values (id, replace_values, comments) VALUES
('yobi1', '[" "]', '备用字段1'),
('torokummdd_id', '["528", "1228"]', '注册编号ID列表'),
('nendo_cource_id', '["2413", "2412"]', '年度课程ID列表'),
('tsumi_kaikubun_id', '["4"]', '积立类别ID'),
('tsumi_kaikubun', '["4"]', '积立类别'),
('tsumi_kinkubun_id', '["2"]', '积立金额类别ID'),
('tsumi_kinkubun', '["2"]', '积立金额类别'),
('boshu_nendo_id', '["2024"]', '募集年度ID'),
('boshu_nendo', '["2024"]', '募集年度'),
('boshu_kaiji_id', '["3"]', '募集开始ID'),
('boshu_kaiji', '["3"]', '募集开始'),
('man_shuzen_kubun_id', '["3"]', '满修正类别ID'),
('err_zandaka_shomei_kibojiki', '["0"]', '余额证明记录类别'),
('err_kumiaino', '["0"]', '组合号码错误标志'),
('hagaki_sofu_ym', '[""]', '明信片送付年月'),
('err_kubun', '["0", "1", "2", "3"]', '"0"：正常 "1"：ワーニング "2"：軽度 "3"：重度'),
('err_sofu_kibokakunin', '["0", "1"]', '送付记录确认'),
('yushiyotei_gengo', '["1", "2", "3", "4"]', '预计语言'),
('gengo_kubun', '["1", "2", "3", "4"]', '语言类别'),
('gengo_eiji', '["M", "T", "S", "H"]', '"M"：明治 "T"：大正 "S"：昭和 "H"：平成'),
('nyuryoku_shorui_flag', '["1", "2"]', '"1"：申込書及び送付先指定依頼書あり "2"：申込書のみ "3"：送付先指定依頼書のみ'),
('irai_naiyokubun', '["1", "2", "3"]', '"1"：新規登録 "2"：変更登録 "3"：送付先指定の中止'),
('sofu_taishoshorui', '["1", "2", "3"]', '"1"：全ての書類 "2"：残高証明書及び買入計算書 注：依頼内容区分=\'3\'(送付先指定の中止)の場合は、スペースとする'),
('kinriyugu_kubun', '["0", "1"]', '"0"：利率優遇商品申込無し "1"：利率優遇商品申込有り'),
('tsumi_riyu', '["1", "2", "3", "4"]', '"1"：大規模修繕 "2"：利回り "3"：機構預り "4"：情報誌'),
('err_sofu_irai_naiyokubun', '["0"]', '错误送付请求内容类别'),
('nyuryoku_tsumi_kaikubun', '["4"]', '输入积立类别'),
('nyuryoku_tsumi_kinkubun', '["2"]', '输入积立金额类别'),
('nyuryoku_torokummdd', '["528", "1228"]', '输入注册编号ID列表'),
('tsumi_kaiseq_id', '["1", "2", "3", "4", "5", "6", "7", "8"]', '积立会序号ID列表'),
('hokankubun', '["1", "2"]', '保管类别'),
('kaigocd', '["1010"]', '介护代码'),
('saiken_hakkohi', '["20241010"]', '债权发布日期'),
('saiken_mankihi', '["20241010"]', '债权满期日期'),
('hakko_nendo_id', '["2024"]', '发货年度ID'),
('ribarai_kaisuseq_id', '["10"]', '利率变更序号ID');


```

- csv_path = csv文件夹/文件路径
- CSVtoPostgresInserter
	- 输入csv_path，调用CSVProcessor处理数据
	- 获取处理后的replace文件夹路径
	- 读取csv插入数据库
- CSVProcessor
	- process_csv，输入csv_path，对文件进行处理后输出到replace文件夹
- config
	- 存储全局参数

