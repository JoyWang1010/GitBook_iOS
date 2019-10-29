# 正则表达式

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>&#x5B57;&#x7B26;</b>
      </th>
      <th style="text-align:left"><b>&#x63CF;&#x8FF0;</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">\</td>
      <td style="text-align:left">&#x5C06;&#x4E0B;&#x4E00;&#x4E2A;&#x5B57;&#x7B26;&#x6807;&#x8BB0;&#x4E3A;&#x4E00;&#x4E2A;&#x7279;&#x6B8A;&#x5B57;&#x7B26;&#x3001;&#x6216;&#x4E00;&#x4E2A;&#x539F;&#x4E49;&#x5B57;&#x7B26;&#x3001;&#x6216;&#x4E00;&#x4E2A;
        &#x5411;&#x540E;&#x5F15;&#x7528;&#x3001;&#x6216;&#x4E00;&#x4E2A;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;n&apos;
        &#x5339;&#x914D;&#x5B57;&#x7B26; &quot;n&quot;&#x3002;&apos;\n&apos; &#x5339;&#x914D;&#x4E00;&#x4E2A;&#x6362;&#x884C;&#x7B26;&#x3002;&#x5E8F;&#x5217;
        &apos;\\&apos; &#x5339;&#x914D; &quot;\&quot; &#x800C; &quot;\(&quot; &#x5219;&#x5339;&#x914D;
        &quot;(&quot;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">^</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x8F93;&#x5165;&#x5B57;&#x7B26;&#x4E32;&#x7684;&#x5F00;&#x59CB;&#x4F4D;&#x7F6E;&#x3002;&#x5982;&#x679C;&#x8BBE;&#x7F6E;&#x4E86;
        RegExp &#x5BF9;&#x8C61;&#x7684; Multiline &#x5C5E;&#x6027;&#xFF0C;^ &#x4E5F;&#x5339;&#x914D;
        &apos;\n&apos; &#x6216; &apos;\r&apos; &#x4E4B;&#x540E;&#x7684;&#x4F4D;&#x7F6E;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">$</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x8F93;&#x5165;&#x5B57;&#x7B26;&#x4E32;&#x7684;&#x7ED3;&#x675F;&#x4F4D;&#x7F6E;&#x3002;&#x5982;&#x679C;&#x8BBE;&#x7F6E;&#x4E86;RegExp
        &#x5BF9;&#x8C61;&#x7684; Multiline &#x5C5E;&#x6027;&#xFF0C;$ &#x4E5F;&#x5339;&#x914D;
        &apos;\n&apos; &#x6216; &apos;\r&apos; &#x4E4B;&#x524D;&#x7684;&#x4F4D;&#x7F6E;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">*</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x524D;&#x9762;&#x7684;&#x5B50;&#x8868;&#x8FBE;&#x5F0F;&#x96F6;&#x6B21;&#x6216;&#x591A;&#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;zo*
        &#x80FD;&#x5339;&#x914D; &quot;z&quot; &#x4EE5;&#x53CA; &quot;zoo&quot;&#x3002;*
        &#x7B49;&#x4EF7;&#x4E8E;{0,}&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">+</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x524D;&#x9762;&#x7684;&#x5B50;&#x8868;&#x8FBE;&#x5F0F;&#x4E00;&#x6B21;&#x6216;&#x591A;&#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;zo+&apos;
        &#x80FD;&#x5339;&#x914D; &quot;zo&quot; &#x4EE5;&#x53CA; &quot;zoo&quot;&#xFF0C;&#x4F46;&#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;z&quot;&#x3002;+ &#x7B49;&#x4EF7;&#x4E8E; {1,}&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">?</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x524D;&#x9762;&#x7684;&#x5B50;&#x8868;&#x8FBE;&#x5F0F;&#x96F6;&#x6B21;&#x6216;&#x4E00;&#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&quot;do(es)?&quot;
        &#x53EF;&#x4EE5;&#x5339;&#x914D; &quot;do&quot; &#x6216; &quot;does&quot;
        &#x4E2D;&#x7684;&quot;do&quot; &#x3002;? &#x7B49;&#x4EF7;&#x4E8E; {0,1}&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">{n}</td>
      <td style="text-align:left">n &#x662F;&#x4E00;&#x4E2A;&#x975E;&#x8D1F;&#x6574;&#x6570;&#x3002;&#x5339;&#x914D;&#x786E;&#x5B9A;&#x7684;
        n &#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;o{2}&apos; &#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;Bob&quot; &#x4E2D;&#x7684; &apos;o&apos;&#xFF0C;&#x4F46;&#x662F;&#x80FD;&#x5339;&#x914D;
        &quot;food&quot; &#x4E2D;&#x7684;&#x4E24;&#x4E2A; o&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">{n,}</td>
      <td style="text-align:left">n &#x662F;&#x4E00;&#x4E2A;&#x975E;&#x8D1F;&#x6574;&#x6570;&#x3002;&#x81F3;&#x5C11;&#x5339;&#x914D;n
        &#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;o{2,}&apos; &#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;Bob&quot; &#x4E2D;&#x7684; &apos;o&apos;&#xFF0C;&#x4F46;&#x80FD;&#x5339;&#x914D;
        &quot;foooood&quot; &#x4E2D;&#x7684;&#x6240;&#x6709; o&#x3002;&apos;o{1,}&apos;
        &#x7B49;&#x4EF7;&#x4E8E; &apos;o+&apos;&#x3002;&apos;o{0,}&apos; &#x5219;&#x7B49;&#x4EF7;&#x4E8E;
        &apos;o*&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">{n,m}</td>
      <td style="text-align:left">m &#x548C; n &#x5747;&#x4E3A;&#x975E;&#x8D1F;&#x6574;&#x6570;&#xFF0C;&#x5176;&#x4E2D;n
        &lt;= m&#x3002;&#x6700;&#x5C11;&#x5339;&#x914D; n &#x6B21;&#x4E14;&#x6700;&#x591A;&#x5339;&#x914D;
        m &#x6B21;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&quot;o{1,3}&quot; &#x5C06;&#x5339;&#x914D;
        &quot;fooooood&quot; &#x4E2D;&#x7684;&#x524D;&#x4E09;&#x4E2A; o&#x3002;&apos;o{0,1}&apos;
        &#x7B49;&#x4EF7;&#x4E8E; &apos;o?&apos;&#x3002;&#x8BF7;&#x6CE8;&#x610F;&#x5728;&#x9017;&#x53F7;&#x548C;&#x4E24;&#x4E2A;&#x6570;&#x4E4B;&#x95F4;&#x4E0D;&#x80FD;&#x6709;&#x7A7A;&#x683C;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">?</td>
      <td style="text-align:left">&#x5F53;&#x8BE5;&#x5B57;&#x7B26;&#x7D27;&#x8DDF;&#x5728;&#x4EFB;&#x4F55;&#x4E00;&#x4E2A;&#x5176;&#x4ED6;&#x9650;&#x5236;&#x7B26;
        (*, +, ?, {n}, {n,}, {n,m}) &#x540E;&#x9762;&#x65F6;&#xFF0C;&#x5339;&#x914D;&#x6A21;&#x5F0F;&#x662F;&#x975E;&#x8D2A;&#x5A6A;&#x7684;&#x3002;&#x975E;&#x8D2A;&#x5A6A;&#x6A21;&#x5F0F;&#x5C3D;&#x53EF;&#x80FD;&#x5C11;&#x7684;&#x5339;&#x914D;&#x6240;&#x641C;&#x7D22;&#x7684;&#x5B57;&#x7B26;&#x4E32;&#xFF0C;&#x800C;&#x9ED8;&#x8BA4;&#x7684;&#x8D2A;&#x5A6A;&#x6A21;&#x5F0F;&#x5219;&#x5C3D;&#x53EF;&#x80FD;&#x591A;&#x7684;&#x5339;&#x914D;&#x6240;&#x641C;&#x7D22;&#x7684;&#x5B57;&#x7B26;&#x4E32;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&#x5BF9;&#x4E8E;&#x5B57;&#x7B26;&#x4E32;
        &quot;oooo&quot;&#xFF0C;&apos;o+?&apos; &#x5C06;&#x5339;&#x914D;&#x5355;&#x4E2A;
        &quot;o&quot;&#xFF0C;&#x800C; &apos;o+&apos; &#x5C06;&#x5339;&#x914D;&#x6240;&#x6709;
        &apos;o&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">.</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x9664; &quot;\n&quot; &#x4E4B;&#x5916;&#x7684;&#x4EFB;&#x4F55;&#x5355;&#x4E2A;&#x5B57;&#x7B26;&#x3002;&#x8981;&#x5339;&#x914D;&#x5305;&#x62EC;
        &apos;\n&apos; &#x5728;&#x5185;&#x7684;&#x4EFB;&#x4F55;&#x5B57;&#x7B26;&#xFF0C;&#x8BF7;&#x4F7F;&#x7528;&#x8C61;
        &apos;[.\n]&apos; &#x7684;&#x6A21;&#x5F0F;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">(pattern)</td>
      <td style="text-align:left">&#x5339;&#x914D; pattern &#x5E76;&#x83B7;&#x53D6;&#x8FD9;&#x4E00;&#x5339;&#x914D;&#x3002;&#x6240;&#x83B7;&#x53D6;&#x7684;&#x5339;&#x914D;&#x53EF;&#x4EE5;&#x4ECE;&#x4EA7;&#x751F;&#x7684;
        Matches &#x96C6;&#x5408;&#x5F97;&#x5230;&#xFF0C;&#x5728;VBScript &#x4E2D;&#x4F7F;&#x7528;
        SubMatches &#x96C6;&#x5408;&#xFF0C;&#x5728;JScript &#x4E2D;&#x5219;&#x4F7F;&#x7528;
        $0&#x2026;$9 &#x5C5E;&#x6027;&#x3002;&#x8981;&#x5339;&#x914D;&#x5706;&#x62EC;&#x53F7;&#x5B57;&#x7B26;&#xFF0C;&#x8BF7;&#x4F7F;&#x7528;
        &apos;\(&apos; &#x6216; &apos;\)&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">(?:pattern)</td>
      <td style="text-align:left">&#x5339;&#x914D; pattern &#x4F46;&#x4E0D;&#x83B7;&#x53D6;&#x5339;&#x914D;&#x7ED3;&#x679C;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x8BF4;&#x8FD9;&#x662F;&#x4E00;&#x4E2A;&#x975E;&#x83B7;&#x53D6;&#x5339;&#x914D;&#xFF0C;&#x4E0D;&#x8FDB;&#x884C;&#x5B58;&#x50A8;&#x4F9B;&#x4EE5;&#x540E;&#x4F7F;&#x7528;&#x3002;&#x8FD9;&#x5728;&#x4F7F;&#x7528;
        &quot;&#x6216;&quot; &#x5B57;&#x7B26; (|) &#x6765;&#x7EC4;&#x5408;&#x4E00;&#x4E2A;&#x6A21;&#x5F0F;&#x7684;&#x5404;&#x4E2A;&#x90E8;&#x5206;&#x662F;&#x5F88;&#x6709;&#x7528;&#x3002;&#x4F8B;&#x5982;&#xFF0C;
        &apos;industr(?:y|ies) &#x5C31;&#x662F;&#x4E00;&#x4E2A;&#x6BD4; &apos;industry|industries&apos;
        &#x66F4;&#x7B80;&#x7565;&#x7684;&#x8868;&#x8FBE;&#x5F0F;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">(?=pattern)</td>
      <td style="text-align:left">&#x6B63;&#x5411;&#x9884;&#x67E5;&#xFF0C;&#x5728;&#x4EFB;&#x4F55;&#x5339;&#x914D;
        pattern &#x7684;&#x5B57;&#x7B26;&#x4E32;&#x5F00;&#x59CB;&#x5904;&#x5339;&#x914D;&#x67E5;&#x627E;&#x5B57;&#x7B26;&#x4E32;&#x3002;&#x8FD9;&#x662F;&#x4E00;&#x4E2A;&#x975E;&#x83B7;&#x53D6;&#x5339;&#x914D;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x8BF4;&#xFF0C;&#x8BE5;&#x5339;&#x914D;&#x4E0D;&#x9700;&#x8981;&#x83B7;&#x53D6;&#x4F9B;&#x4EE5;&#x540E;&#x4F7F;&#x7528;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;Windows
        (?=95|98|NT|2000)&apos; &#x80FD;&#x5339;&#x914D; &quot;Windows 2000&quot;
        &#x4E2D;&#x7684; &quot;Windows&quot; &#xFF0C;&#x4F46;&#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;Windows 3.1&quot; &#x4E2D;&#x7684; &quot;Windows&quot;&#x3002;&#x9884;&#x67E5;&#x4E0D;&#x6D88;&#x8017;&#x5B57;&#x7B26;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x8BF4;&#xFF0C;&#x5728;&#x4E00;&#x4E2A;&#x5339;&#x914D;&#x53D1;&#x751F;&#x540E;&#xFF0C;&#x5728;&#x6700;&#x540E;&#x4E00;&#x6B21;&#x5339;&#x914D;&#x4E4B;&#x540E;&#x7ACB;&#x5373;&#x5F00;&#x59CB;&#x4E0B;&#x4E00;&#x6B21;&#x5339;&#x914D;&#x7684;&#x641C;&#x7D22;&#xFF0C;&#x800C;&#x4E0D;&#x662F;&#x4ECE;&#x5305;&#x542B;&#x9884;&#x67E5;&#x7684;&#x5B57;&#x7B26;&#x4E4B;&#x540E;&#x5F00;&#x59CB;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">(?!pattern)</td>
      <td style="text-align:left">&#x8D1F;&#x5411;&#x9884;&#x67E5;&#xFF0C;&#x5728;&#x4EFB;&#x4F55;&#x4E0D;&#x5339;&#x914D;
        pattern &#x7684;&#x5B57;&#x7B26;&#x4E32;&#x5F00;&#x59CB;&#x5904;&#x5339;&#x914D;&#x67E5;&#x627E;&#x5B57;&#x7B26;&#x4E32;&#x3002;&#x8FD9;&#x662F;&#x4E00;&#x4E2A;&#x975E;&#x83B7;&#x53D6;&#x5339;&#x914D;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x8BF4;&#xFF0C;&#x8BE5;&#x5339;&#x914D;&#x4E0D;&#x9700;&#x8981;&#x83B7;&#x53D6;&#x4F9B;&#x4EE5;&#x540E;&#x4F7F;&#x7528;&#x3002;&#x4F8B;&#x5982;&apos;Windows
        (?!95|98|NT|2000)&apos; &#x80FD;&#x5339;&#x914D; &quot;Windows 3.1&quot;
        &#x4E2D;&#x7684; &quot;Windows&quot;&#xFF0C;&#x4F46;&#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;Windows 2000&quot; &#x4E2D;&#x7684; &quot;Windows&quot;&#x3002;&#x9884;&#x67E5;&#x4E0D;&#x6D88;&#x8017;&#x5B57;&#x7B26;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x8BF4;&#xFF0C;&#x5728;&#x4E00;&#x4E2A;&#x5339;&#x914D;&#x53D1;&#x751F;&#x540E;&#xFF0C;&#x5728;&#x6700;&#x540E;&#x4E00;&#x6B21;&#x5339;&#x914D;&#x4E4B;&#x540E;&#x7ACB;&#x5373;&#x5F00;&#x59CB;&#x4E0B;&#x4E00;&#x6B21;&#x5339;&#x914D;&#x7684;&#x641C;&#x7D22;&#xFF0C;&#x800C;&#x4E0D;&#x662F;&#x4ECE;&#x5305;&#x542B;&#x9884;&#x67E5;&#x7684;&#x5B57;&#x7B26;&#x4E4B;&#x540E;&#x5F00;&#x59CB;</td>
    </tr>
    <tr>
      <td style="text-align:left">x|y</td>
      <td style="text-align:left">&#x5339;&#x914D; x &#x6216; y&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;z|food&apos;
        &#x80FD;&#x5339;&#x914D; &quot;z&quot; &#x6216; &quot;food&quot;&#x3002;&apos;(z|f)ood&apos;
        &#x5219;&#x5339;&#x914D; &quot;zood&quot; &#x6216; &quot;food&quot;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">[xyz]</td>
      <td style="text-align:left">&#x5B57;&#x7B26;&#x96C6;&#x5408;&#x3002;&#x5339;&#x914D;&#x6240;&#x5305;&#x542B;&#x7684;&#x4EFB;&#x610F;&#x4E00;&#x4E2A;&#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;
        &apos;[abc]&apos; &#x53EF;&#x4EE5;&#x5339;&#x914D; &quot;plain&quot; &#x4E2D;&#x7684;
        &apos;a&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">[^xyz]</td>
      <td style="text-align:left">
        <p>&#x8D1F;&#x503C;&#x5B57;&#x7B26;&#x96C6;&#x5408;&#x3002;&#x5339;&#x914D;&#x672A;&#x5305;&#x542B;&#x7684;&#x4EFB;&#x610F;&#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;
          &apos;[^abc]&apos; &#x53EF;&#x4EE5;&#x5339;&#x914D; &quot;plain&quot; &#x4E2D;&#x7684;&apos;p&apos;&#x3002;</p>
        <p>&#x610F;&#x6307;&#x5426;&#x5B9A;&#x6216;&#x4E0D;&#x5339;&#x914D;&#x62EC;&#x53F7;&#x91CC;&#x5185;&#x5BB9;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">[a-z]</td>
      <td style="text-align:left">&#x5B57;&#x7B26;&#x8303;&#x56F4;&#x3002;&#x5339;&#x914D;&#x6307;&#x5B9A;&#x8303;&#x56F4;&#x5185;&#x7684;&#x4EFB;&#x610F;&#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;[a-z]&apos;
        &#x53EF;&#x4EE5;&#x5339;&#x914D; &apos;a&apos; &#x5230; &apos;z&apos; &#x8303;&#x56F4;&#x5185;&#x7684;&#x4EFB;&#x610F;&#x5C0F;&#x5199;&#x5B57;&#x6BCD;&#x5B57;&#x7B26;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">[^a-z]</td>
      <td style="text-align:left">&#x8D1F;&#x503C;&#x5B57;&#x7B26;&#x8303;&#x56F4;&#x3002;&#x5339;&#x914D;&#x4EFB;&#x4F55;&#x4E0D;&#x5728;&#x6307;&#x5B9A;&#x8303;&#x56F4;&#x5185;&#x7684;&#x4EFB;&#x610F;&#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;[^a-z]&apos;
        &#x53EF;&#x4EE5;&#x5339;&#x914D;&#x4EFB;&#x4F55;&#x4E0D;&#x5728; &apos;a&apos;
        &#x5230; &apos;z&apos; &#x8303;&#x56F4;&#x5185;&#x7684;&#x4EFB;&#x610F;&#x5B57;&#x7B26;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\b</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x5355;&#x8BCD;&#x8FB9;&#x754C;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x6307;&#x5355;&#x8BCD;&#x548C;&#x7A7A;&#x683C;&#x95F4;&#x7684;&#x4F4D;&#x7F6E;&#x3002;&#x4F8B;&#x5982;&#xFF0C;
        &apos;er\b&apos; &#x53EF;&#x4EE5;&#x5339;&#x914D;&quot;never&quot; &#x4E2D;&#x7684;
        &apos;er&apos;&#xFF0C;&#x4F46;&#x4E0D;&#x80FD;&#x5339;&#x914D; &quot;verb&quot;
        &#x4E2D;&#x7684; &apos;er&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\B</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x975E;&#x5355;&#x8BCD;&#x8FB9;&#x754C;&#x3002;&apos;er\B&apos;
        &#x80FD;&#x5339;&#x914D; &quot;verb&quot; &#x4E2D;&#x7684; &apos;er&apos;&#xFF0C;&#x4F46;&#x4E0D;&#x80FD;&#x5339;&#x914D;
        &quot;never&quot; &#x4E2D;&#x7684; &apos;er&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\cx</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x7531; x &#x6307;&#x660E;&#x7684;&#x63A7;&#x5236;&#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C;
        \cM &#x5339;&#x914D;&#x4E00;&#x4E2A; Control-M &#x6216;&#x56DE;&#x8F66;&#x7B26;&#x3002;x
        &#x7684;&#x503C;&#x5FC5;&#x987B;&#x4E3A; A-Z &#x6216; a-z &#x4E4B;&#x4E00;&#x3002;&#x5426;&#x5219;&#xFF0C;&#x5C06;
        c &#x89C6;&#x4E3A;&#x4E00;&#x4E2A;&#x539F;&#x4E49;&#x7684; &apos;c&apos;
        &#x5B57;&#x7B26;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\d</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x6570;&#x5B57;&#x5B57;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        [0-9]&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\D</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x975E;&#x6570;&#x5B57;&#x5B57;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        [^0-9]&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\f</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x6362;&#x9875;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        \x0c &#x548C; \cL&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\n</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x6362;&#x884C;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        \x0a &#x548C; \cJ&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\r</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x56DE;&#x8F66;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        \x0d &#x548C; \cM&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\s</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4EFB;&#x4F55;&#x7A7A;&#x767D;&#x5B57;&#x7B26;&#xFF0C;&#x5305;&#x62EC;&#x7A7A;&#x683C;&#x3001;&#x5236;&#x8868;&#x7B26;&#x3001;&#x6362;&#x9875;&#x7B26;&#x7B49;&#x7B49;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        [ \f\n\r\t\v]&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\S</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4EFB;&#x4F55;&#x975E;&#x7A7A;&#x767D;&#x5B57;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        [^ \f\n\r\t\v]&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\t</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x5236;&#x8868;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        \x09 &#x548C; \cI&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\v</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4E00;&#x4E2A;&#x5782;&#x76F4;&#x5236;&#x8868;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        \x0b &#x548C; \cK&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\w</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x5305;&#x62EC;&#x4E0B;&#x5212;&#x7EBF;&#x7684;&#x4EFB;&#x4F55;&#x5355;&#x8BCD;&#x5B57;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;&apos;[A-Za-z0-9_]&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\W</td>
      <td style="text-align:left">&#x5339;&#x914D;&#x4EFB;&#x4F55;&#x975E;&#x5355;&#x8BCD;&#x5B57;&#x7B26;&#x3002;&#x7B49;&#x4EF7;&#x4E8E;
        &apos;[^A-Za-z0-9_]&apos;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\xn</td>
      <td style="text-align:left">&#x5339;&#x914D; n&#xFF0C;&#x5176;&#x4E2D; n &#x4E3A;&#x5341;&#x516D;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;&#x3002;&#x5341;&#x516D;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;&#x5FC5;&#x987B;&#x4E3A;&#x786E;&#x5B9A;&#x7684;&#x4E24;&#x4E2A;&#x6570;&#x5B57;&#x957F;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;\x41&apos;
        &#x5339;&#x914D; &quot;A&quot;&#x3002;&apos;\x041&apos; &#x5219;&#x7B49;&#x4EF7;&#x4E8E;
        &apos;\x04&apos; &amp; &quot;1&quot;&#x3002;&#x6B63;&#x5219;&#x8868;&#x8FBE;&#x5F0F;&#x4E2D;&#x53EF;&#x4EE5;&#x4F7F;&#x7528;
        ASCII &#x7F16;&#x7801;&#x3002;.</td>
    </tr>
    <tr>
      <td style="text-align:left">\num</td>
      <td style="text-align:left">&#x5339;&#x914D; num&#xFF0C;&#x5176;&#x4E2D; num &#x662F;&#x4E00;&#x4E2A;&#x6B63;&#x6574;&#x6570;&#x3002;&#x5BF9;&#x6240;&#x83B7;&#x53D6;&#x7684;&#x5339;&#x914D;&#x7684;&#x5F15;&#x7528;&#x3002;&#x4F8B;&#x5982;&#xFF0C;&apos;(.)\1&apos;
        &#x5339;&#x914D;&#x4E24;&#x4E2A;&#x8FDE;&#x7EED;&#x7684;&#x76F8;&#x540C;&#x5B57;&#x7B26;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\n</td>
      <td style="text-align:left">&#x6807;&#x8BC6;&#x4E00;&#x4E2A;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;&#x6216;&#x4E00;&#x4E2A;&#x5411;&#x540E;&#x5F15;&#x7528;&#x3002;&#x5982;&#x679C;
        \n &#x4E4B;&#x524D;&#x81F3;&#x5C11; n &#x4E2A;&#x83B7;&#x53D6;&#x7684;&#x5B50;&#x8868;&#x8FBE;&#x5F0F;&#xFF0C;&#x5219;
        n &#x4E3A;&#x5411;&#x540E;&#x5F15;&#x7528;&#x3002;&#x5426;&#x5219;&#xFF0C;&#x5982;&#x679C;
        n &#x4E3A;&#x516B;&#x8FDB;&#x5236;&#x6570;&#x5B57; (0-7)&#xFF0C;&#x5219;
        n &#x4E3A;&#x4E00;&#x4E2A;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\nm</td>
      <td style="text-align:left">&#x6807;&#x8BC6;&#x4E00;&#x4E2A;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;&#x6216;&#x4E00;&#x4E2A;&#x5411;&#x540E;&#x5F15;&#x7528;&#x3002;&#x5982;&#x679C;
        \nm &#x4E4B;&#x524D;&#x81F3;&#x5C11;&#x6709; nm &#x4E2A;&#x83B7;&#x5F97;&#x5B50;&#x8868;&#x8FBE;&#x5F0F;&#xFF0C;&#x5219;
        nm &#x4E3A;&#x5411;&#x540E;&#x5F15;&#x7528;&#x3002;&#x5982;&#x679C; \nm
        &#x4E4B;&#x524D;&#x81F3;&#x5C11;&#x6709; n &#x4E2A;&#x83B7;&#x53D6;&#xFF0C;&#x5219;
        n &#x4E3A;&#x4E00;&#x4E2A;&#x540E;&#x8DDF;&#x6587;&#x5B57; m &#x7684;&#x5411;&#x540E;&#x5F15;&#x7528;&#x3002;&#x5982;&#x679C;&#x524D;&#x9762;&#x7684;&#x6761;&#x4EF6;&#x90FD;&#x4E0D;&#x6EE1;&#x8DB3;&#xFF0C;&#x82E5;
        n &#x548C; m &#x5747;&#x4E3A;&#x516B;&#x8FDB;&#x5236;&#x6570;&#x5B57; (0-7)&#xFF0C;&#x5219;
        \nm &#x5C06;&#x5339;&#x914D;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;
        nm&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\nml</td>
      <td style="text-align:left">&#x5982;&#x679C; n &#x4E3A;&#x516B;&#x8FDB;&#x5236;&#x6570;&#x5B57; (0-3)&#xFF0C;&#x4E14;
        m &#x548C; l &#x5747;&#x4E3A;&#x516B;&#x8FDB;&#x5236;&#x6570;&#x5B57; (0-7)&#xFF0C;&#x5219;&#x5339;&#x914D;&#x516B;&#x8FDB;&#x5236;&#x8F6C;&#x4E49;&#x503C;
        nml&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">\un</td>
      <td style="text-align:left">&#x5339;&#x914D; n&#xFF0C;&#x5176;&#x4E2D; n &#x662F;&#x4E00;&#x4E2A;&#x7528;&#x56DB;&#x4E2A;&#x5341;&#x516D;&#x8FDB;&#x5236;&#x6570;&#x5B57;&#x8868;&#x793A;&#x7684;
        Unicode &#x5B57;&#x7B26;&#x3002;&#x4F8B;&#x5982;&#xFF0C; \u00A9 &#x5339;&#x914D;&#x7248;&#x6743;&#x7B26;&#x53F7;
        (?)&#x3002;</td>
    </tr>
  </tbody>
</table>**正则表达式元字符与转义**

**正则表达式中具有特殊含义的字符称之为元字符，常用的元字符有**：

\ 一般用于转义字符

^ 断言目标的开始位置\(或在多行模式下是行首\)

$ 断言目标的结束位置\(或在多行模式下是行尾\)

\[ 开始字符类定义

\] 结束字符类定义

\| 开始一个可选分支\( 子组的开始标记\) 子组的结束标记

? 作为量词，表示 0 次或 1 次匹配。位于量词后面用于改变量词的贪婪特性。 \(查阅量词\)

\* 量词，0 次或多次匹配

+ 量词，1 次或多次匹配

{ 自定义量词开始标记

} 自定义量词结束标记

. 匹配除换行符外的任何字符\(默认\)

\i不区分大小写字母的搜索

\w匹配字母或数字或下划线

\s匹配任意的空白符，包括空格、制表符、换行符

\d数字

//下面的\s匹配任意的空白符，包括空格，制表符，换行符。\[^\s\]代表非空白符。\[^\s\]+表示一次或多次匹配非空白符。

$p = '/^我\[^\s\]+\(苹果\|香蕉\)$/';

$str = "我喜欢吃苹果";

if \(preg\_match\($p, $str\)\) {

    echo '匹配成功';

}

元字符具有两种使用场景，一种是可以在任何地方都能使用，另一种是只能在方括号内使用，在方括号内使用的有：

\ 转义字符^ 仅在作为第一个字符\(方括号内\)时，表明字符类取反- 标记字符范围

其中^在反括号外面，表示断言目标的开始位置，但在方括号内部则代表字符类取反，方括号内的减号-可以标记字符范围，例如0-9表示0到9之间的所有数字。

//下面的\w匹配字母或数字或下划线。

$p = '/\[\w\.\-\]+@\[a-z0-9\-\]+\.\(com\|cn\)/';

$str = "我的邮箱是Spark.eric@imooc.com";

preg\_match\($p, $str, $match\);

echo $match\[0\];

贪婪模式与懒惰模式

正则表达式中每个元字符匹配一个字符，当使用+之后将会变的贪婪，它将匹配尽可能多的字符，但使用问号?字符时，它将尽可能少的匹配字符，既是懒惰模式。

贪婪模式：在可匹配与可不匹配的时候，优先匹配

//下面的\d表示匹配数字

$p = '/\d+\-\d+/';

$str = "我的电话是010-12345678";

preg\_match\($p, $str, $match\);

echo $match\[0\]; //结果为：010-12345678

懒惰模式：在可匹配与可不匹配的时候，优先不匹配

$p = '/\d?\-\d?/';

$str = "我的电话是010-12345678";

preg\_match\($p, $str, $match\);

echo $match\[0\];  //结果为：0-1

当我们确切的知道所匹配的字符长度的时候，可以使用{}指定匹配字符数

$p = '/\d{3}\-\d{8}/';

$str = "我的电话是010-12345678";

preg\_match\($p, $str, $match\);

echo $match\[0\]; //结果为：010-12345678

