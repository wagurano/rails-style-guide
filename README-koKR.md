# 서문 

> 롤 모델이 중요하다. <br/>
> -- 경찰관  알렉스 제이. 머피 / 로보캅

지침(가이드)는 루비 온 레일스 4로 개발할 때 모범 사례와 
스타일 처방전을 묶어서 보여주는 것을 목표로 한다.
이미 커뮤니티가 만들어둔 루비 코딩 스타일 가이드
([영문](https://github.com/bbatsov/ruby-style-guide), [한글](https://github.com/dalzony/ruby-style-guide/blob/master/README-koKR.md))를 보충한다.

지침 중 일부는 레일스 4.0 이상에서만 사용할 수 있다.

이 문서를 PDF나 HTML로 만들려면 
[트랜스뮤터](https://github.com/TechnoGate/transmuter)를 사용할 수 있다.

지침(가이드)를 다른 언어로 번역한 문서는 아래와 같다:

* [러시아어](https://github.com/arbox/rails-style-guide/blob/master/README-ruRU.md)
* [중국어 간체자](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhCN.md)
* [중국어 정자](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhTW.md)
* [영어](https://github.com/bbatsov/rails-style-guide/blob/master/README.md)
* [일본어](https://github.com/satour/rails-style-guide/blob/master/README-jaJA.md)
* [터키어](https://github.com/tolgaavci/rails-style-guide/blob/master/README-trTR.md)
* [한국어](https://github.com/wagurano/rails-style-guide/blob/translate_ko/README-koKR.md)


# 레일스 스타일 가이드

이 문서는 모범 사례를 제시하여 현실의 레일스 프로그래머가 코드를 작성하면 
그 코드를 다른 레일스 프로그래머가 수정할 수 
있도록 한다. 스타일 가이드는 자주 사용하는 현실의 사용법을 반영하고, 스타일 가이드는 
사람들이 거부한 이상을 지키고 위험한 상황을 자주 겪지 않도록 해야 한다. &ndash; 그것이 옳은 
것이라도

이 문서 안에는 여러 가지 구역으로 연관된 규칙을 나뉜다. 규칙 아래에
이유를 그 이유를 달았다(매우 당연한 경우는 
생략하였다).

규칙은 어느날 갑자기 만들어지지 않았다 - 규칙은 소프트웨어 엔지니어 전문가로서의 
폭 넓은 경험과 레일스 커뮤니티의 구성원들의 피드백과 제안, 그리고 추천받은 
레일스 프로그래밍 참고자료 여러가지를 
바탕으로 한다. 

## 순서

* [환경설정](#환경설정)
* [라우팅](#라우팅)
* [콘트롤러](#콘트롤러)
* [모델](#모델)
* [마이그레이션](#마이그레이션)
* [뷰](#뷰)
* [다국어지원](#다국어지원)
* [애셋](#애셋)
* [이메일](#이메일)
* [번들러](#번들러)
* [바꿔야할 젬](#바꿔야할-젬)
* [프로세스 관리](#프로세스-관리)

## 환경설정

* <a name="config-initializers"></a>
  초기화 관련 코드는 `config/initializers` 디렉토리 아래에 있다. 초기화 코드는
  애플리케이션을 시작할 때 실행한다.
<sup>[[link](#config-initializers)]</sup>

* <a name="gem-initializers"></a>
  젬마다 초기화 코드는 젬이름과 같게 파일을 만든다.
  예를 들어 `carrierwave.rb`, `active_admin.rb`, 등과 같다.
<sup>[[link](#gem-initializers)]</sup>

* <a name="dev-test-prod-configs"></a>
  개발, 테스트, 프로덕션 환경에 따라 환경을 설정하라.
  (해당 파일은 `config/environments/` 디렉토리 아래에 있다)
<sup>[[link](#dev-test-prod-configs)]</sup>

  * (애샛 파일이 있으면) 프리컴파일할 때 추가하도록 파일 이름을 나열하라: 

    ```Ruby
    # config/environments/production.rb
    # Precompile additional assets (application.js, application.css,
    #and all non-JS/CSS are already added)
    config.assets.precompile += %w( rails_admin/rails_admin.css rails_admin/rails_admin.js )
    ```

* <a name="app-config"></a>
  모든 환경에 공통으로 적용할 설정 내용은 
  `config/application.rb` 파일에 있다.
<sup>[[link](#app-config)]</sup>

* <a name="staging-like-prod"></a>
  `production` 환경과 매우 비슷한 `staging` 환경을 
  만들어라.
<sup>[[link](#staging-like-prod)]</sup>

## 라우팅

* <a name="member-collection-routes"></a>
  레스트풀 리소스에 액션을 추가하려면 
  `member` 와 `collection` 라우트를 사용하라.
<sup>[[link](#member-collection-routes)]</sup>

  ```Ruby
  # 나쁜 예
  get 'subscriptions/:id/unsubscribe'
  resources :subscriptions

  # 좋은 예
  resources :subscriptions do
    get 'unsubscribe', on: :member
  end

  # 나쁜 예
  get 'photos/search'
  resources :photos

  # 좋은 예
  resources :photos do
    get 'search', on: :collection
  end
  ```

* <a name="many-member-collection-routes"></a>
  `member/collection` 라우트를 여러개 정의하려면 
  블록 구문을 사용하라.
<sup>[[link](#many-member-collection-routes)]</sup>

  ```Ruby
  resources :subscriptions do
    member do
      get 'unsubscribe'
      # more routes
    end
  end

  resources :photos do
    collection do
      get 'search'
      # more routes
    end
  end
  ```

* <a name="nested-routes"></a>
  라우트 안에 라우트를 사용하면 액티브렉코드 모델의 관계를 분명히 나타낼 수 
  있다.
<sup>[[link](#nested-routes)]</sup>

  ```Ruby
  class Post < ActiveRecord::Base
    has_many :comments
  end

  class Comments < ActiveRecord::Base
    belongs_to :post
  end

  # routes.rb
  resources :posts do
    resources :comments
  end
  ```

* <a name="namespaced-routes"></a>
  네임스페이스를 사용해서 해당 액션을 묶어라.
<sup>[[link](#namespaced-routes)]</sup>

  ```Ruby
  namespace :admin do
    # Directs /admin/products/* to Admin::ProductsController
    # (app/controllers/admin/products_controller.rb)
    resources :products
  end
  ```

* <a name="no-wild-routes"></a>
  아래와 같이 라우트를 예전 방식으로 하면 안된다. 아래와 같은 라우트는 GET 메소드로 모든 콘틀롤러의 
  액션을 리퀘스트할 수 있다.
<sup>[[link](#no-wild-routes)]</sup>

  ```Ruby
  # 너무 나쁜 예
  match ':controller(/:action(/:id(.:format)))'
  ```

* <a name="no-match-routes"></a>
  라우트를 정의할 때 `match` 사용할 경우는 액션 하나에 `[:get, :post, :patch, :put, :delete]` 과 같은 리퀘스트 메소드 여러 개를 `:via` 옵션으로 나열할 때이다.
<sup>[[link](#no-match-routes)]</sup>

## 콘트롤러

* <a name="skinny-controllers"></a>
  콘트롤러의 코드는 짧게 작성하라 - 콘틀롤러는 뷰에서 데이트를 가져오기만 하고 
  비지니스 로직을 넣지 않도록 하라(모든 비지니스 로직은
  모델에 있는 것이 타당하다).
<sup>[[link](#skinny-controllers)]</sup>

* <a name="one-method"></a>
  콘트롤러 액션마다 find 또는 new 메소드를 제외하고 메소드 하나만 실행하는 편이
  적절하다.
<sup>[[link](#one-method)]</sup>

* <a name="shared-instance-variables"></a>
  콘트롤러와 뷰에서 사용할 인스턴스 변수를 두 개 이상 사용하지 마라.
<sup>[[link](#shared-instance-variables)]</sup>

## 모델

* <a name="model-classes"></a>
  액티브 레코드가 아닌 모델을 도입하는데 주저하지 마라.
<sup>[[link](#model-classes)]</sup>

* <a name="meaningful-model-names"></a>
  모델 이름은 뜻을 함축하더라도 
  줄임말을 쓰지 마라. 
<sup>[[link](#meaningful-model-names)]</sup>

* <a name="activeattr-gem"></a>
  객체가 액티브 레코드처럼 동작할 필요가 
  있을 때
  (예. 데이터 베이스 저장/수정 하기전 모델 속성 값 검사) ActiveAttr](https://github.com/cgriego/active_attr)
  젬을 쓴다.
<sup>[[link](#activeattr-gem)]</sup>

  ```Ruby
  class Message
    include ActiveAttr::Model

    attribute :name
    attribute :email
    attribute :content
    attribute :priority

    attr_accessible :name, :email, :content

    validates :name, presence: true
    validates :email, format: { with: /\A[-a-z0-9_+\.]+\@([-a-z0-9]+\.)+[a-z0-9]{2,4}\z/i }
    validates :content, length: { maximum: 500 }
  end
  ```

  더 많은 예시는 
  [레일스 캐스트](http://railscasts.com/episodes/326-activeattr)를 참고하라. 

### 액티브 레코드

* <a name="keep-ar-defaults"></a>
  부득이하게 액티브 레코드가 정해둔 (테이블 이름, 프라이머리 키, 등) 것을 바꿀 때는
  타당한 이유가 있어야 한다. (예. 데이터베이스에 대한 권한이 없을 때)
<sup>[[link](#keep-ar-defaults)]</sup>

  ```Ruby
  # 나쁜 예 - 테이터베이스 테이블 스키마를 아래처럼 수정하지 마라.
  class Transaction < ActiveRecord::Base
    self.table_name = 'order'
    ...
  end
  ```

* <a name="macro-style-methods"></a>
  매크로처럼 사용하는 메소드 (`has_many`, `validates`, 등)를 클래스를 정의하는
  앞 부분에 모아두어라. 
<sup>[[link](#macro-style-methods)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    # keep the default scope first (if any)
    default_scope { where(active: true) }

    # constants come up next
    COLORS = %w(red green blue)

    # afterwards we put attr related macros
    attr_accessor :formatted_date_of_birth

    attr_accessible :login, :first_name, :last_name, :email, :password

    # followed by association macros
    belongs_to :country

    has_many :authentications, dependent: :destroy

    # and validation macros
    validates :email, presence: true
    validates :username, presence: true
    validates :username, uniqueness: { case_sensitive: false }
    validates :username, format: { with: /\A[A-Za-z][A-Za-z0-9._-]{2,19}\z/ }
    validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true}

    # next we have callbacks
    before_save :cook
    before_save :update_username_lower

    # other macros (like devise's) should be placed after the callbacks

    ...
  end
  ```

* <a name="has-many-through"></a>
  `has_many :through`를 `has_and_belongs_to_many`보다 추천한다. `has_many
  :through`는 속성을 추가하거나 데이터베이스 테이블을 조인할 모델에 대해 유효성 검증을 할 수 있다. 
<sup>[[link](#has-many-through)]</sup>

  ```Ruby
  # 추천하지 않음 - has_and_belongs_to_many 사용
  class User < ActiveRecord::Base
    has_and_belongs_to_many :groups
  end

  class Group < ActiveRecord::Base
    has_and_belongs_to_many :users
  end

  # 추천하는 방법 - has_many :through 사용
  class User < ActiveRecord::Base
    has_many :memberships
    has_many :groups, through: :memberships
  end

  class Membership < ActiveRecord::Base
    belongs_to :user
    belongs_to :group
  end

  class Group < ActiveRecord::Base
    has_many :memberships
    has_many :users, through: :memberships
  end
  ```

* <a name="read-attribute"></a>
  `self[:attribute]`를 `read_attribute(:attribute)`보다 추천한다.
<sup>[[link](#read-attribute)]</sup>

  ```Ruby
  # 나쁜 예
  def amount
    read_attribute(:amount) * 100
  end

  # 좋은 예
  def amount
    self[:amount] * 100
  end
  ```

* <a name="write-attribute"></a>
  `self[:attribute] = value`를 `write_attribute(:attribute, value)`보다 추천한다.
<sup>[[link](#write-attribute)]</sup>

  ```Ruby
  # 나쁜 예
  def amount
    write_attribute(:amount, 100)
  end

  # 좋은 예
  def amount
    self[:amount] = 100
  end
  ```

* <a name="sexy-validations"></a>
  앞으로는 ["섹시한"
  유효성 검증 방법](http://thelucid.com/2010/01/08/sexy-validation-in-edge-rails-rails-3/)을 사용하라.
<sup>[[link](#sexy-validations)]</sup>

  ```Ruby
  # 나쁜 예
  validates_presence_of :email

  # 좋은 예
  validates :email, presence: true
  ```

* <a name="custom-validator-file"></a>
  프로그래머가 만든 유효성 검증 방법을 한 번 이상 실행하거나 유효성 검증 방법이
  정규표현식을 쓸 때, 소스 파일을 따로 작성한다.
<sup>[[link](#custom-validator-file)]</sup>

  ```Ruby
  # 나쁜 예
  class Person
    validates :email, format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i }
  end

  # 좋은 예
  class EmailValidator < ActiveModel::EachValidator
    def validate_each(record, attribute, value)
      record.errors[attribute] << (options[:message] || 'is not a valid email') unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
    end
  end

  class Person
    validates :email, email: true
  end
  ```

* <a name="app-validators"></a>
  프로그래머가 만든 유효성 검증하는 소스 파일을 `app/validators` 디렉토리에 둔다.
<sup>[[link](#app-validators)]</sup>

* <a name="custom-validators-gem"></a>
  프로그래머가 만든 유효성 검증하는 파일을 젬으로 만들어서 관련있는 여러 앱을 유지관리하거나
  일반적으로 사용하는데 부족하지 않은지 고려한다.
<sup>[[link](#custom-validators-gem)]</sup>

* <a name="named-scopes"></a>
  스코프 뒤에 이름을 붙여 사용하는데 주저하지 마라.
<sup>[[link](#named-scopes)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    scope :active, -> { where(active: true) }
    scope :inactive, -> { where(active: false) }

    scope :with_orders, -> { joins(:orders).select('distinct(users.id)') }
  end
  ```

* <a name="named-scope-class"></a>
  람다와 파라미터로 정의하는 스코프가 점점 복잡해지면, 
  클래스 메소드를 쓰는 방법보다 스코프를 지정하는 목적이 같도록 제공하고 
  `ActiveRecord::Relation` 객체를 반환하는 편이 더 나아 보인다. 
  의견이 갈리지만 스코프를 다음과 같이 간략하게 정의할 수 있다.
  When a named scope defined with a lambda and parameters becomes too
  complicated, it is preferable to make a class method instead which serves the
  same purpose of the named scope and returns an `ActiveRecord::Relation`
  object. Arguably you can define even simpler scopes like this.
<sup>[[link](#named-scope-class)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    def self.with_orders
      joins(:orders).select('distinct(users.id)')
    end
  end
  ```

* <a name="beware-update-attribute"></a>
  [`update_attribute`](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update_attribute) 
  메소드 
  실행결과를 주의하라. 모델 밸리데이션을 실행하지 (`update_attributes` 와 달리, 단복수 주의) 않아서 
  부적합한 상태가 되기 쉽다.
<sup>[[link](#beware-update-attribute)]</sup>

* <a name="user-friendly-urls"></a>
  사람이 읽기 쉬운 URL을 사용하라. `id`외에 모델 속성을 URL에 넣어서 
  모델을 보여준다. 보여주는 방법 몇가지는 다음과 같다: 
<sup>[[link](#user-friendly-urls)]</sup>

  * 모델의 `to_param`를 재정의한다. 레일스에서는 이 메소드를 객체의 URL을 
    구성하는데 사용한다. 재정의하지 않으면 레코드의 
    `id`를 문자열로 반환한다. 속성을 추가하여 사람이 읽기 
    쉽게 메소드를 재정의할 수 있다. 

      ```Ruby
      class Person
        def to_param
          "#{id} #{name}".parameterize
        end
      end
      ```

  URL에 알맞은 값으로 변환하려면, 문자열의 `parameterize` 메소드를 실행해야 한다. 
  객체의 `id`를 맨 앞에 나타나도록 해야 액티브 레코드의 `find` 메소드로 
  객체를 가져올 수 있다.

  * `friendly_id` 젬을 사용하라. 모델의 `id`가 아닌 모델을 나타낼 수 있는 
    속성 몇개를 사용하여 사람이 읽기 쉬운 URL을 만들 수 있다. 

      ```Ruby
      class Person
        extend FriendlyId
        friendly_id :name, use: :slugged
      end
      ```

  젬 사용법에 대한 정보는 [젬 사용설명서](https://github.com/norman/friendly_id)를 
  참고한다.

* <a name="find-each"></a>
  `find_each` 메소드를 사용하여 액티브렉코드 객체를 담은 콜렉션을 For Loop 처럼 
  객체 하나씩 가져와서 처리할 수 있다. 테이터베이스( `all` 메소드를 사용한 경우)에서 
  레코드를 담은 콜렉션 안을 순서대로 도는 방법이 비효율적인 이유는 한번에 모든 객체를 생성하려고 하기 때문이다. 
  그러한 경우, 일괄처리하는 메소드는 메모리를 적게 사용하여 
  레코드를 처리할 수 있다.
<sup>[[link](#find-each)]</sup>


  ```Ruby
  # 나쁜 예
  Person.all.each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').each do |person|
    person.party_all_night!
  end

  # 좋은 예
  Person.find_each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').find_each do |person|
    person.party_all_night!
  end
  ```

* <a name="before_destroy"></a>
  [레일스가 종속 관계에 따라 콜백을 만든다](https://github.com/rails/rails/issues/3458)를 참고하면,
  매번
  `before_destroy` 콜백을 실행하여 `prepend: true` 옵션이 붙은 유효성 검증을 실행한다.

  ```Ruby
  # 나쁜 예 super_admin? 결과 참이라도 옵션에 따라서 roles를 연이어 삭제해버린다)
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end

  # 좋은 예
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable, prepend: true

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end
  ```


## 마이그레이션

* <a name="schema-version"></a>
  소스 버전 콘트롤에서 `schema.rb` (또는 `structure.sql`) 파일을 제외하지 마라.
<sup>[[link](#schema-version)]</sup>

* <a name="db-schema-load"></a>
  `rake db:schema:load`를 `rake db:migrate` 대신 실행하여 비어 있는 데이터베이스를
  초기 설정한다.
<sup>[[link](#db-schema-load)]</sup>

* <a name="default-migration-values"></a>
  Enforce default values in the migrations themselves instead of in the
  application layer.
<sup>[[link](#default-migration-values)]</sup>

  ```Ruby
  # 나쁜 예 - application enforced default value
  def amount
    self[:amount] or 0
  end
  ```

  While enforcing table defaults only in Rails is suggested by many
  Rails developers, it's an extremely brittle approach that
  leaves your data vulnerable to many application bugs.  And you'll
  have to consider the fact that most non-trivial apps share a
  database with other applications, so imposing data integrity from
  the Rails app is impossible.

* <a name="foreign-key-constraints"></a>
  반드시 외부참조-키 제약조건을 사용하라. 액티브 레코드가 제약조건을 지원하지 않지만
  다음 서드-파티 젬 몇가지는 매우 유용하다.
  [schema_plus](https://github.com/lomba/schema_plus), 
  [foreigner](https://github.com/matthuhiggins/foreigner).
<sup>[[link](#foreign-key-constraints)]</sup>

* <a name="change-vs-up-down"></a>
  마이그레이션으로 테이블이나 컬럼을 추가할 때,
  `up`, `down` 메소드보다 `change` 메소를 사용하라.
<sup>[[link](#change-vs-up-down)]</sup>

  ```Ruby
  # 오래된 방법
  class AddNameToPeople < ActiveRecord::Migration
    def up
      add_column :people, :name, :string
    end

    def down
      remove_column :people, :name
    end
  end

  # 새로 추천하는 방법
  class AddNameToPeople < ActiveRecord::Migration
    def change
      add_column :people, :name, :string
    end
  end
  ```

* <a name="no-model-class-migrations"></a>
  Don't use model classes in migrations. The model classes are constantly
  evolving and at some point in the future migrations that used to work might
  stop, because of changes in the models used.
<sup>[[link](#no-model-class-migrations)]</sup>

## 뷰

* <a name="no-direct-model-view"></a>
  뷰에서 모델 레이어를 직접 사용하지 마라.
<sup>[[link](#no-direct-model-view)]</sup>

* <a name="no-complex-view-formatting"></a>
  뷰의 포맷을 복잡하게 사용하지 말고, 뷰 헬퍼나 모델의 메소드를 따로 빼서 포맷을
  처리하라.
<sup>[[link](#no-complex-view-formatting)]</sup>

* <a name="partials"></a>
  중복 코드를 줄이기 위해 파셜과 레이아웃을 사용하라.
<sup>[[link](#partials)]</sup>

## 다국어지원

* <a name="locale-texts"></a>
  문자열이나 지역 설정 정보는 뷰, 모델, 콘트롤러 안에 두지 않는 편이 좋다.
  텍스트는 `config/locales` 디렉토리 아래에 
  두는 편이 좋다.
<sup>[[link](#locale-texts)]</sup>

* <a name="translated-labels"></a>
  액티브 레코드 모델의 레이블을 설명하여 표시하려면,
  `activerecord` 스코프를 사용하라:
<sup>[[link](#translated-labels)]</sup>

  ```
  en:
    activerecord:
      models:
        user: Member
      attributes:
        user:
          name: 'Full name'
  ```

  `User.model_name.human`는 "Member"를 반환하겠고 
  `User.human_attribute_name("name")`는 "Full name"를 반환하겠다.
  이처럼 모델 속성에 대한 설명을 뷰에서 사용하겠다.


* <a name="organize-locale-files"></a>
  뷰에서 사용하는 텍스트를 액티브 레코드 속성 설명과 분리하라.
  `models` 폴더에 모델의 지역 설정 파일을 두고
  `views` 폴더에 뷰에서 사용하는 텍스트를 둔다.
<sup>[[link](#organize-locale-files)]</sup>

  * 지역 설정 파일을 디렉토리에 추가 배치하려면,
    디렉토리 이름을 `application.rb` 파일에 순서대로 
    나열합니다.

      ```Ruby
      # config/application.rb
      config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}')]
      ```

* <a name="shared-localization"></a>
  일자 또는 통화에 대한 지역 설정 옵션을 공유하려면,
  `locale` 디렉토리에 파일을 둔다.
<sup>[[link](#shared-localization)]</sup>

* <a name="short-i18n"></a>
  I18n 메소드는 `I18n.translate`는 짧게 `I18n.t`로 사용하고
  `I18n.localize`는 짧게 `I18n.l`를 사용하라.
<sup>[[link](#short-i18n)]</sup>

* <a name="lazy-lookup"></a>
  뷰에서 사용하는 텍스트를 "게으르게" 찾는 방식을 사용하라.
  아래와 같은 구조가 있다고 하자:
<sup>[[link](#lazy-lookup)]</sup>

  ```
  en:
    users:
      show:
        title: 'User details page'
  ```

  `users.show.title`에 해당하는 문자열은 
  아래와 같이 `app/views/users/show.html.haml` 템플릿 파일에서 찾을 수 있다:

  ```Ruby
  = t '.title'
  ```

* <a name="dot-separated-keys"></a>
  콘트롤러와 모델에서 에서 키를 점을 찍어서 나열하고 
  `:scope` 오션을 사용하지 않는다. 점으로 찍으면 계층구조를 따라서
  읽기 쉽다.
<sup>[[link](#dot-separated-keys)]</sup>

  ```Ruby
  # 이와 같이 사용하고 
  I18n.t 'activerecord.errors.messages.record_invalid'

  # 이와 같이 사용하지 않는다.
  I18n.t :record_invalid, :scope => [:activerecord, :errors, :messages]
  ```

* <a name="i18n-guides"></a>
  레일스 i18n에 대한 정보를 더 얻으려면 
  [레일스 가이드](http://guides.rubyonrails.org/i18n.html)를 참조하라.
<sup>[[link](#i18n-guides)]</sup>

## 애셋

[애셋 파이프라인](http://guides.rubyonrails.org/asset_pipeline.html)을 사용하여 애플레이션을 내부적으로
더욱 결속한다.

* <a name="reserve-app-assets"></a>
  `app/assets` 아래에 추가할 스타일시트, 자바스크립트, 이미지 파일을 둔다.
<sup>[[link](#reserve-app-assets)]</sup>

* <a name="lib-assets"></a>
  `lib/assets`아래는 라이브러리가 사용하는 파일을 두며 프로그래머가 만든 라이브러리라고 애플리케이션의 스코프에 딱 맞는 건 아니다.
<sup>[[link](#lib-assets)]</sup>

* <a name="vendor-assets"></a>
  [jQuery](http://jquery.com/) 또는 
  [부트스트랩](http://twitter.github.com/bootstrap/) 같은 써드 파티 코드는 
  `vendor/assets` 아래에서 파일을 둔다.
<sup>[[link](#vendor-assets)]</sup>

* <a name="gem-assets"></a>
  가능하면, 애샛을 젬으로 묶어서 만든 버전을 사용하라(예, 
  [jquery-rails](https://github.com/rails/jquery-rails),
  [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails),
  [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass),
  [zurb-foundation](https://github.com/zurb/foundation)).
<sup>[[link](#gem-assets)]</sup>

## 메일러

* <a name="mailer-name"></a>
  `SomethingMailer`처럼 메일러 이름을 사용하라. Mailer 라고 뒤에 붙이지
  않으면 메일러의 한 종류인지 분명하지 않고 메일러에 해당하는 뷰도 
  분명하지 않다.
<sup>[[link](#mailer-name)]</sup>

* <a name="html-plain-email"></a>
  HTML 과 텍스트만 있는 뷰 템플릿 둘다 준비하라.
<sup>[[link](#html-plain-email)]</sup>

* <a name="enable-delivery-errors"></a>
  개발 모드에서는 메일을 전송할 때 실패하면 에러가 발생하도록 옵션을 켜라.
  보통 에러 옵션은 꺼져 있다.
<sup>[[link](#enable-delivery-errors)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.raise_delivery_errors = true
  ```

* <a name="local-smtp"></a>
  개발 모드에서는 [Mailcatcher](https://github.com/sj26/mailcatcher)처럼 
  개발 PC를 SMTP 서버로 
  사용하라.
<sup>[[link](#local-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.smtp_settings = {
    address: 'localhost',
    port: 1025,
    # more settings
  }
  ```

* <a name="default-hostname"></a>
  기본 설정으로 호스트 네임을 준비하라.
<sup>[[link](#default-hostname)]</sup>

  ```Ruby
  # config/environments/development.rb
  config.action_mailer.default_url_options = { host: "#{local_ip}:3000" }

  # config/environments/production.rb
  config.action_mailer.default_url_options = { host: 'your_site.com' }

  # in your mailer class
  default_url_options[:host] = 'your_site.com'
  ```

* <a name="url-not-path-in-email"></a>
  이메일에 사이트 주소를 넣으려면, `_url` 메소드는 쓰고, 
  `_path`메소드는 쓰지 않는다. `_url` 메소도는 호스트 네임이 들어가지만 
  `_path` 메소드는 들어가지 않는다.
<sup>[[link](#url-not-path-in-email)]</sup>

  ```Ruby
  # 나쁜 예
  다음 링크는 본 교유과정(course)에 관한 정보를 제공합니다.
  = link_to 'here', course_path(@course)

  # 좋은 예
  다음 링크는 본 교유과정(course)에 관한 정보를 제공합니다.
  = link_to 'here', course_url(@course)
  ```

* <a name="email-addresses"></a>
  보내는 사람과 받는 사람의 이메일 주소를 올바른 형식으로 맞춰라. 다음과 같은 형식을 사용하라:
<sup>[[link](#email-addresses)]</sup>

  ```Ruby
  # in your mailer class
  default from: 'Your Name <info@your_site.com>'
  ```

* <a name="delivery-method-test"></a>
  테스트 모드에서 이메일을 발송하는 메소드는 `test`로 설정하라:
<sup>[[link](#delivery-method-test)]</sup>

  ```Ruby
  # config/environments/test.rb

  config.action_mailer.delivery_method = :test
  ```

* <a name="delivery-method-smtp"></a>
  개발과 배포 모드에서는 이메일 발송 메소드는 `smtp`로 설정한다:
<sup>[[link](#delivery-method-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb, config/environments/production.rb

  config.action_mailer.delivery_method = :smtp
  ```

* <a name="inline-email-styles"></a>
  html 이메일을 보낼 때 모든 스타일 시트를 이메일 문서에 넣어야 하는데, 일부 메일 클라이언트가 
  메일에 스타일 시트를 포함하지 있지 않으면 에러가 발생하기 때문이다. 문제가 발생하기에 html 이메일을 관리하기 
  어려워지고 중복되는 코드가 늘어난다. 다음 젬 두 개는 비슷하게도 스타일 시트를 수정하여 
  html 태그에 맞게 넣어준다: 
  [premailer-rails](https://github.com/fphilipe/premailer-rails) 과
  [roadie](https://github.com/Mange/roadie).
<sup>[[link](#inline-email-styles)]</sup>

* <a name="background-email"></a>
  이메일을 보내는 동안 페이지를 렌더링하지 않도록 한다. 이메일을 여러 통 
  발송할 때 페이지를 불러오는데 시간이 걸려서 타임아웃이 걸릴 수 있다. 
  타임아웃에 걸리지 않으려면 [sidekiq](https://github.com/mperham/sidekiq) 젬을 써서 
  백그라운드로 이메일을 발송하여 처리할 수 있다.
<sup>[[link](#background-email)]</sup>

## 번들러

* <a name="dev-test-gems"></a>
  개발 모드나 테스트 모드에서만 사용하는 젬은 그룹으로 분리해서
  젬 파일을 넣어라.
<sup>[[link](#dev-test-gems)]</sup>

* <a name="only-good-gems"></a>
  사람들이 많이 쓰는 젬만 프로젝트에 사용하라. 덜 알려진 젬을 프로젝트에 사용하려고 검토하고 
  있다면 우선 젬의 소스코드부터 차분히 
  살펴보는 편이 좋겠다.
<sup>[[link](#only-good-gems)]</sup>

* <a name="os-specific-gemfile-locks"></a>
  운영체제에 따라 쓰거나 쓸 수 없는 젬은 프로젝트 안의 `Gemfile.lock` 안에서 계속해서 바뀌면서 
  나타는데 개발자마다 다른 운영체제를 사용하기 때문이다. 
  OS X 에서만 사용하는 젬은 Gemfile의 `darwin` 그룹에 넣고, 
  리눅스에서만 사용하는 젬은 `linux` 그룹에 넣어라:
<sup>[[link](#os-specific-gemfile-locks)]</sup>

  ```Ruby
  # Gemfile
  group :darwin do
    gem 'rb-fsevent'
    gem 'growl'
  end

  group :linux do
    gem 'rb-inotify'
  end
  ```

  해당 환경에 적합한 젬을 규정하려면, 아래와 같이 
  `config/application.rb` 파일에 추가하라:

  ```Ruby
  platform = RUBY_PLATFORM.match(/(linux|darwin)/)[0].to_sym
  Bundler.require(platform)
  ```

* <a name="gemfile-lock"></a>
  `Gemfile.lock` 파일을 버전관리 시스템에서 제외하지 않아야 한다. 이 파일은 
  임시로 생성하지 않는다 - 팀원 모두가 `bundle install` 실행할 때 
  같은 젬 버전을 사용하도록 한다.
<sup>[[link](#gemfile-lock)]</sup>

## 바꿔야할 젬

아래 목록은 문제가 있거나 바꿔야 젬이다. 
아래 젬들은 가급적 포함하지 않는 편이 좋겠다. 

* [rmagick](http://rmagick.rubyforge.org/) - 이 젬음 
  메모리 사용을 많이 하기로 유명해서, 
  [minimagick](https://github.com/minimagick/minimagick)으로 바꿔라.

* [autotest](http://www.zenspider.com/ZSS/Products/ZenTest/) - 테스트를 
  자동으로 실행하는 옛날 방법. 다음 젬에 한참 못 미친다.
  [guard](https://github.com/guard/guard)와 
  [watchr](https://github.com/mynyml/watchr).

* [rcov](https://github.com/relevance/rcov) - 소프트웨어 테스트와 관계 있는 코드 커버리지 툴,
  루비 1.9와 호환되지 않음. [SimpleCov](https://github.com/colszowka/simplecov)
  로 바꿔라.

* [therubyracer](https://github.com/cowboyd/therubyracer) - 이 젬을 
  프로덕션 모드에서 사용한다면 메모리를 엄청나게 필요해서 매우 실망한다. 
  대신 `node.js`를 사용하기를 추천한다.  

위 목록은 계속 추가하겠다. 널리 쓰였지만 바꿔야할 젬이 있으면 
알려 주길 바란다.

## 프로세스 관리

* <a name="foreman"></a>
  외부 프로세스 여러 개 위에서 프로젝트를 돌린다면, 
  [foreman](https://github.com/ddollar/foreman)으로 프로세스를 관리하라.
<sup>[[link](#foreman)]</sup>

# 다음에 읽을 목록

레일스 스타일에 관한 매우 훌륭한 참고 도서 몇권은, 여러분이 시간날 때 
읽어야 한다고 생각한다: 

* [The Rails 4 Way](http://www.amazon.com/The-Rails-Addison-Wesley-Professional-Ruby/dp/0321944275)
* [Ruby on Rails Guides](http://guides.rubyonrails.org/)
* [The RSpec Book](http://pragprog.com/book/achbd/the-rspec-book)
* [The Cucumber Book](http://pragprog.com/book/hwcuc/the-cucumber-book)
* [Everyday Rails Testing with RSpec](https://leanpub.com/everydayrailsrspec)
* [Better Specs for RSpec](http://betterspecs.org)

# 기여 방법

본 가이드는 비석에 글을 새기듯이 작성하지 않았다. 레일스 코딩 스타일에 관심있는
여러분과 함께 공유하고 싶은 마음에
결국 리소스를 만들어서 루비 커뮤니티 전체에 널리 쓰이리라고 생각한다.

티켓을 열거나 풀 리퀘스트를 보내서 개선하는데 망설이지 않아도 된다.
여러분이 도와주기를 바란다. 

본 프로젝트와 루보캅을 [깃팁](https://www.gittip.com/bbatsov)을 통해 돈으로 기부하여 
응원할 수 있다.

[![깃팁으로 응원하는 방법](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

## 기여하는 방법은?

다음 링크 [기여방법에 관한 지침](https://github.com/bbatsov/rails-style-guide/blob/master/CONTRIBUTING.md)를 타고 들어간다.

# 라이선스

![크레이티브 커먼즈 라이선스](http://i.creativecommons.org/l/by/3.0/88x31.png)
이 저작물은 크리에티브 커먼즈 이용허락규약 3.0 언포티드
라이선스([영어](http://creativecommons.org/licenses/by/3.0/deed.en_US), [한국어](http://creativecommons.org/licenses/by/3.0/deed.ko))을 따릅니다.

# 세계로 전파

커뮤니티가 이끌어가는 스타일 가이드는 사용하는 사람 수가 적으면 커뮤니티는 그것이 있는지 
모른다. 이 문서에 대한 트윗을 남기고, 친구와 직장 동료에게 공유하라. 
모든 댓글, 건의 사항, 의견은 이 문서를 점점 발전하게 한다. 
우리는 가장 좋은 가이드를 원하지 않은가?

감사합니다,<br/>
[보즈히다르(Bozhidar)](https://twitter.com/bbatsov)