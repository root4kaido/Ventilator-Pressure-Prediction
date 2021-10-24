# Ventilator-Pressure-Prediction

[Notion](https://www.notion.so/Google-Brain-Ventilator-Pressure-Prediction-74763728a1e54b0997350e859dd34d3e)

- 01…LGBM ベースライン
- 02…LSTM ベースライン
- 03…LSTM 01の特徴量で．
- 04…LSTM．R, Cはカテゴリとして扱ったほうがいいかと思ったらそうでもなさそう
- 05…ロバストスケーラーはだめそう．LSTMの層を増やすのもダメそう．get_window_featureもだめそう．
- 06…u_out=0だけのaggはだめそう．RCのonehotもだめそう．でかいほうが差が大きいので，MSEにしてみたけどだめそう．
- 07…前半25個だけ(確実にu_out=0だけを使う)もだめそう．
- 08…u_in, pressureをlogにするのもだめそう．lossを全体で計算するようにしてみた．パブリックのノートブックのCV異様に低いのはこのせいっぽい．u_out=1にしぼると0.25になる
- 09…trainingはもしかして08のほうがいいのか…？→だめそう．スケジューラちょっと変更．→だめそう
- 10…CNN．だめそう．
- 11…get_work_features．だめそう．u_inの2階微分 かわらなそう．
- 12…R, Cで圧力のgroupby_describeを取得→ダメそう．u_idでも同じことする→だめそう．
- 13…ゴミ
- 14…コサインアニーリング→変わらなそう
- 15…RとCをR_Cにしてカテゴリ化→ちびっと改善
- 16…LSTMを2層に→0.005くらい改善
- 17…1DCNN→ちょっと悪化．もうちょい検討しても良いかも．カレーちゃんさんの1DCNNはメチャ効いた！差は何…？
- 18…Agumentation．u_inだけ．ちょっと悪化．もうちょい検討しても良いかも．
- 19…u_in_diffmaxとu_in_diffmean加えた．0.01くらい改善．
- 20…19の特徴量で，17のネットワーク．
- 21…oofで悪いbreath_idぬく．だめそう．R=50抜く．微妙にダメそう．
- 22…u_in, u_outごとにgroupbyして統計量．だめそう．
- 23…pseudo．R!=50を使った．収束は早いが最終的なスコアは微妙に悪くなった．pretrainとしてつかってもみたけどだめ．
- 24…TCN.だめそう&おそい．
- 25…fft系．だめそう．ピーク．ダメそう．
- 26…u_inに係数掛けたり，pressureに係数掛けてみたりした．ダメそう．
- 27…augmentその2．u_inだけじゃなくて，その他のu_inを用いている特徴にもaugmentした．無いのと同じくらいの精度．
- 28…pressureのちょん切られているものを補正したもので学習→あとでclip．微妙にダメそう．
- 29…クラスターを追加→ダメそう
- 30…CosineAnnealingWarmRestartsに変えた．ちょっとよくなった．
- 31…R_Cでstratifiedした．
- 32…ロバストスケーラー．微妙．
- 33…戸田さんノートブック．2のほうが，戸田さんのやつ．なんもないのは自分の構成．
- 34…cumsumのshift．だめそう．
- 35…再度戸田さんのやつカスタム．2のほうが，まわせるようになったやつ．
- 36…戸田さんの特徴量+自分ので，regression．ちょっとよくなった．
- 37…戸田さんのclassificationを，pytorch lightningに．37_2のほうで完成．
- 38…classification．ちょっと特徴量追加．ちょっとよくなった．
- 39…移動平均とったpressure．全然効かなくて草．u_inの平滑化を特徴量にしたらすこしきいた．
- 40…num_layerかえたり．だめそう．dropoutは0.1のがよかった
- 41…u_inの平滑化のlag．めちゃびみょう．
- 42…focal loss 行けるかと思ったけどダメそう
- 43…pressureを前のやつとのdiffにして，予測させてみた．ダメでした．
- 44…lrかえた
- 45…カテゴリをR_Cにした
- 46…ベストの5fold
- 47…平滑化をもうちょっと長い単位にした．あと，ちょっとpytorch lightning使うと精度落ちる気がしたので確認．やはり使わないほうが精度が良い…(47_2)
- 48…平滑化をローパスフィルタにした．こっちのがわかりやすいし．精度的には47とほぼ同じくらい．
- 49…平滑化をu_out=0だけにしたけど，だめでした．
- 50…lrかえた．
- 51…u_in - 平滑化　だめそう．agg追加．びっみょうによくなった
- 52…weight_decayを1e-3→0.1にした．ちょびっとよくなったが，まだ過学習している．
- 53…5foldまわした．あんま良くなってなかった．かなしい．
- 54…ルート追加．だめそう．でもlagとかいれたらちょっと効いた．
- 55…微分？追加ちょっと効いた．
- 56…QuantileTransformerだめ．つまりゴミ
- 57…5fold
- 58…戸田さんloss．めちょきいた
- 59…crossentropy+mae*1/2．きいた．
- 60…58と59を合体．
- 61…1DCNN追加はやっぱり無理．5fold．
- 62…0.9:0.1でu_out=1も追加．だめそう．lowpassのcumsum．ちょっときいた．100epochにしたらさがっちゃったね．
- 63…順番変えたらすこしさがっちゃった…どうしようかな．→なんとかした．
- 64…high pass だめっぽい．
- 65…戸田さんloss調整しようとしたけどダメでした．→5fold
- 66…再現性が取れないのを色々試行錯誤した
- 67…5fold
- 68…良さげな初期化に変更．だめそう．LSTMの後にバッチノームはだめ．Layerノームはわずかにいいかも．
- 69…SELU．だめそう．main_modeをカテゴリで追加．メチャ効いた．
- 70…5fold
- 71…70が何故か下がってしまったので，31のようにfoldを変えた．そしたら，69は入れない方がスコア良かった
- 72…u_in_mean_half追加．だめ．sine．ちょっときいた．
- 73…LSTMを減らす形にした．メチャきいた．
- 74…headのmlp→1DCNNに変更．だめそう．
- 75…5fold．
- 76…初期化をけろっぴさんに変更．だめそう．u_in_halfの後半だけのagg追加．だめそう．1DCNNのちゃんと初期化．びみょにだめそう．LSTMのあとにattention層追加．びみょにだめそう．aggにmedianも追加．だめそう．
- 77…mlp→1DCNNで5fold．
- 78…外れ値っぽいの外すのはダメそう．aggはルートとローパスに対しても良いかも.だめでした．
- 79…lagを-4までつかう．意味なさそう．
- 80…スケジューラの検討．ちょっぴりワームアップの期間を短くしたら効いた．シグモイドを使った自作ロスはだめだった．
- 81…80で伸びたのを5fold回したけど伸びなくて辛いね
- 82…u_in_rateなるものを追加
- 83…smooth l1 lossにかえた．ちょっとのびた．
- 84…エポック数検討．80でいいかも．
- 85…5fold
- 86…lossの検討．最後maeだけにすれば伸びると思ったがダメだった．つらい．最後10epochをmaeのみにしたらちょっときいた

memo 

まるちkfoldかつ，それぞれのR_Cをクラスタリングして，クラスタが均等に割り振られるようにするのが良いかも．
wavenet
l1 smooth loss
u_in meanでクラスタリングして，統計値取る→一個のクラスタ間で教師なしやったら層ごとに別れる
lstm→transformer


あのbreath_idははずれちでは→そうでもないのかなあ
スケジューラこねくりまわす．
fftの特徴量
順序つきクラス分類
pp tainのメチャ近いu_inの軌道をつかう．
エポック数も吟味したほうが良いかも．
ちょん切られてる部分