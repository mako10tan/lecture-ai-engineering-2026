# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：
  [search_materials]
    第1〜3回の本講義資料を検索し、関連ページの本文・タイトル・ページ番号を返す。queryに調べたい内容を指定する。
    引数: {"query": {"type": "string"}}
  [make_quiz]
    指定した回の用語から1つを選び、確認問題と答えを返す。「第2回の確認問題を出して」のように、復習用の問題を求められたときに使う。
    引数: {"lecture": {"type": "integer"}}

- STEP 2 の [tool call]：
  [tool call]   glossary_search_glossary({'query': '3レベル'})

- STEP 3 の [tool call]：
  [tool call]   fetch_fetch({'url': 'https://modelcontextprotocol.io/'})

- STEP 4 の [tool call]：
  [tool call]   aws_aws___search_documentation({'search_phrase': 'Amazon S3 versioning overview', 'topics': ['general']})

- 気づき・疑問：
  簡単に機能追加できてしまうが故に、危険と隣り合わせであることを意識しておくことの重要性を認識できました。
  MCPサーバーのツールを選択する部分や、MCPサーバーに投げるリクエストの内容は、事前に決定論的に指定するものではなく、
  いったんLLMで解析して、ツール選択やMCPサーバー向けリクエスト内容を確定していると思われるが、そのあたりの細かな挙動が
  把握しきれていない点が懸念です。特に、ツールの選択を誤ってしまうようなことがあると、意図しない処理が行われてしまうことを
  危惧しています。

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎
- やったこと：
    ツールをもう 1 つ公開する
- 結果：（演習と宿題を同時に実施しているため、変更対象は正しい）
    [変更を確認]
      $ git status
      On branch lec06
      Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git restore <file>..." to discard changes in working directory)
              modified:   app/mcp_server.py
              modified:   app/mcp_servers.json
              modified:   lectures/06/answer.md

      no changes added to commit (use "git add" and/or "git commit -a")
   
    [一覧を見る]（演習時点に比べて、以下の内容が増えている）
      $ uv run python -m app.mcp_agent --list-tools
      [glossary_get_lecture_summary]
        指定した回（1始まりの整数）の講義の要点を返す。
  
       「第3回では何を学ぶ？」のように、回の内容や関係を聞かれたときに使う。
        引数: {"lecture": {"title": "Lecture", "type": "integer"}}
    
    [質問する]
      $ uv run python -m app.mcp_agent --trace "第2回では何を学びますか？"
      [server] glossary: コマンドで起動（uv run python -m app.mcp_server）
      [server] fetch: コマンドで起動（uvx mcp-server-fetch==2026.11.11 --no-readability-js）
      [server] aws: URL に接続（https://knowledge-mcp.global.api.aws）
      [model] gemini-3.5-flash-lite
      [tools] MCP サーバー（app/mcp_servers.json）
      第2回では、**「ファインチューニングとLoRA」**をテーマに学びます。

      主な内容は以下の通りです：

      1. **事前学習とファインチューニング**
         - 事前学習とファインチューニングの違い、それぞれが向く場面・向かない場面の理解。
      2. **SFT（教師ありファインチューニング）**
         - 入力と望ましい出力のペアを使った学習データの作り方と重要性。
      3. **PEFTとLoRAの仕組み**
         - すべてのパラメータを更新するのではなく、小さな低ランク行列（LoRA）だけを学習させることで、メモリや容量を大幅に削減する仕組みの理解。
      4. **QLoRAとの関係や過学習の注意点**
         - 4bit量子化を行うQLoRAの特徴や、過学習、ハルシネーションへの注意。
      5. **演習**
         - Google Colab上で小さなモデル（Qwen3.5-0.8Bなど）を架空のFAQデータでLoRA学習させ、学習前後での出力の変化を体験する。

      関連する回：第2回

      --- trace ---
      [tool call]   glossary_get_lecture_summary({'lecture': 2})
      [tool return] glossary_get_lecture_summary -> # 第2回 ファインチューニングとLoRA

      ## この回のねらい

      - 事前学習とファインチューニングの違い、ファインチューニングが向く場面と向かない場面を理解する
      - LoRAの考え方を理解し、Google Colab上で小さなモデルを…
      [usage] 入力 5,942 tokens / 出力 270 tokens / モデル応答 2 回
    
    [テストする]
      $ uv run pytest -q
      .........................                                                                                                                                     [100%]
      25 passed in 4.47s

- 気づき・疑問：
  演習だけでは体験できなかった、MCPサーバー側の追加について、ステップを学ぶことができたのがよかったです。
  Pythonプログラムを書き換えると、次の実行から変更が有効になっているのは、小さく変えて小さく試すを
  進めやすくてよかったです。こうした案件では、一度に大きく変えるのではなく、小さな改善を積み上げることが
  大切であると感じました。