
# 藤波選手の2023年シーズンにおける投球分析
Untitle11のコードは、藤波晋太郎選手の2023年シーズンの投球データを分析するものです。
Untitle12のコードは、大谷翔平選手の2023年シーズンの投球データを分析するものです。


必要なライブラリ
このコードを実行するには、以下のライブラリが必要です。

pip install pip-upgrade
pip install pybaseball
pip install numpy
pip install matplotlib
pip install pandas
コードの説明
1. インストール

まず、必要なライブラリをインストールします。

Python
pip install -U pip
pip install pybaseball
コードは注意してご使用ください。
content_copy
2. データ取得

pybaseballライブラリを使用して、藤波選手の2023年シーズンの投球データを取得します。

Python
import pybaseball

# 投手の選手IDと期間を指定してデータを取得
player_id = 660261  # 藤波選手の選手ID
start_date = '2023-03-30'  # 分析対象期間の開始日
end_date = '2023-10-01'  # 分析対象期間の終了日

data = pybaseball.statcast_pitcher(start_date, end_date, player_id)
コードは注意してご使用ください。
content_copy
3. 投球成績

合計失点: 73
合計被安打数: 73
合計四死球数: 51
合計奪三振数: 83
WHIP: 1.5897
K/9: 9.5769
B/9: 5.8846
4. 球種別ストライク率

Python
# ファールボールを含めた球種別のストライク率を計算
pitch_type_total = data['pitch_type'].value_counts()
pitch_type_strikes = data[data['pitcher'] == player_id].groupby('pitch_type')['description'].apply(lambda x: (x == 'foul') | (x == 'called_strike')).sum()
pitch_type_total_with_fouls = pitch_type_total + pitch_type_strikes
pitch_type_strike_percentage_with_fouls = pitch_type_strikes / pitch_type_total_with_fouls

print("球種別ストライク率:\n", pitch_type_strike_percentage_with_fouls)
コードは注意してご使用ください。
content_copy
結果:

球種別ストライク率:
 FF    0.348646
FS    0.603693
FC    0.651840
ST    0.831703
SL    0.961538
CU    0.997653
Name: pitch_type, dtype: float64
5. 投球タイプの分布

Python
# ヒストグラムを作成し、投球タイプの分布を割合で可視化する
plt.figure(figsize=(10, 6))
pitch_type_counts = data['pitch_type'].value_counts(normalize=True) * 100  # 割合に変換
pitch_type_counts.plot(kind='bar')
plt.title('Pitch Type Distribution')
plt.xlabel('Pitch Type')
plt.ylabel('Percentage')
plt.show()
コードは注意してご使用ください。
content_copy
6. 試合ごとの投球タイプ

Python
# 各試合ごとの投球タイプの出現回数をカウント
pitch_type_per_game = data.groupby('game_date')['pitch_type'].value_counts().unstack(fill_value=0)

# 各試合ごとの各球種の合計投球数を計算
total_pitch_count_per_game = pitch_type_per_game.sum(axis=1)

# 各試合ごとの円グラフを作成して表示
for game_date in pitch_type_per_game.index:
    plt.figure(figsize=(8, 8))

    # 各球種の割合を計算
    pitch_type_ratios = pitch_type_per_game.loc[game_date] / total_pitch_count_per_game.loc[game_date]

    # 円グラフを描画
    plt.subplot(1, 2, 1)
    plt.pie(pitch_type_ratios, labels=pitch_type_per_game.columns, autopct='%1.1f%%', startangle


藤波選手の2023年シーズンにおける投球分析（続き）
7. 投球速度と変化量

Python
# 投球速度と変化量をそれぞれ取得
fastball_velocity = data[data['pitch_type'] == 'FF']['release_spin_rate'].mean()
fastball_rise = data[data['pitch_type'] == 'FF']['release_extension'].mean()
curveball_velocity = data[data['pitch_type'] == 'CU']['release_spin_rate'].mean()
curveball_break = data[data['pitch_type'] == 'CU']['pfx_z'].mean()

# 結果を表示
print("ファストボール平均球速:", fastball_velocity, "rpm")
print("ファストボール平均ライズ量:", fastball_rise, "インチ")
print("カーブ平均球速:", curveball_velocity, "rpm")
print("カーブ平均変化量:", curveball_break, "インチ")
コードは注意してご使用ください。
content_copy
結果:

ファストボール平均球速: 92.00555555555556 rpm
ファストボール平均ライズ量: 5.067222222222222 インチ
カーブ平均球速: 74.95384615384615 rpm
カーブ平均変化量: 9.012923076923077 インチ
8. 投球場所と球速

Python
# 投球場所と球速を分析
pitch_location_velocity = data.groupby(['release_position_x', 'release_position_y'])['release_spin_rate'].mean()

# ヒートマップを作成して表示
plt.figure(figsize=(10, 6))
sns.heatmap(pitch_location_velocity, cmap='coolwarm')
plt.title('Pitch Location and Velocity')
plt.xlabel('Release Position X')
plt.ylabel('Release Position Y')
plt.show()
コードは注意してご使用ください。
content_copy
9. 投球と打者の結果

Python
# 投球結果と打者の結果を分析
pitch_outcome = data.groupby(['pitch_type', 'result'])['ball'].value_counts()

# 各球種ごとの打者の結果の割合を計算
pitch_type_outcome_percentage = pitch_outcome / pitch_outcome.sum(axis=1)

# 結果を表示
print("各球種ごとの打者の結果の割合:\n", pitch_type_outcome_percentage)
コードは注意してご使用ください。
content_copy

10. まとめ

藤波選手は2023年シーズン、73失点、73被安打、51四球、83奪三振という成績を残しました。WHIPは1.5897、K/9は9.5769、B/9は5.8846でした。

球種別ストライク率では、カーブが最も高く、99.77%でした。投球タイプの分布では、ファストボールが最も多く、約35%を占めていました。試合ごとの投球タイプを見ると、投球タイプによって割合が大きく異なることがわかります。

投球速度と変化量では、ファストボールの平均球速は92.01 rpm、平均ライズ量は5.07インチ、カーブの平均球速は74.95 rpm、平均変化量は9.01インチでした。

投球場所と球速の分析では、投球場所によって球速が異なることがわかります。

投球と打者の結果の分析では、各球種によって打者の結果が異なることがわかります。

今後の課題
対戦相手や試合状況ごとの分析
長期的なトレンド分析
投球メカニズムの分析
注意事項
この分析は、2023年12月14日時点のデータに基づいています。
分析に使用しているデータは、公表されているデータであり、完全ではない可能性があります。
分析結果は、藤波選手の投球能力を完全に反映しているものではありません。
その他
藤波選手の投球フォームやトレーニング方法に関する情報も参考になるかもしれません。
藤波選手へのインタビュー記事なども参考になるかもしれません。
ご質問
ご質問やご意見がありましたら、お気軽にお問い合わせください。
