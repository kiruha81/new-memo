# グループ機能 管理者付与して管理者がメールを送る
##### https://qiita.com/ki_87/items/eb2d073c2dccbed42c5a
##### の続き

## Model
#### group
#### group_user

## db
#### groups
##### t.string :name t.text :introduction t.integer :owner_id

#### group_user
##### t.integer :user_id t.integer :group_id

## アソシエーション
#### user.rb

    has_many :group_users, dependent: :destroy

#### group.rb

    belongs_to :owner, class_name: 'User'
    has_many :group_user, dependent: :destroy
    has_many :users, through: :group_users, source: :user
##### is_owned_by?メソッド

    def is_owned_by?(user)
      owner.id == user.id
    end

#### group_user.rb

    belongs_to :user
    belongs_to :group

## controller
#### groups_controller.rb

    class GroupsController < ApplicationController
      before_action :authenticate_user!
      before_action :ensure_correct_user, only: [:edit, :update]

      def new
        @group =Group.new
      end

      def index
        @book = Book.new
        @groups = Group.all
      end

      def show
        @book = Book.new
        @group = Group.find(params[:id])
      end

      def create
        @group = Group.new(group_params)
        @group.owner_id = current_user.id
        if @group.save
          redirect_to groups_path
        else
          render 'new'
        end
      end

      def edit
      end

      def update
        if @group.update(group_params)
        redirect_to groups_path
      else
        render "edit"
      end
    end

      private

      def group_params
        params.require(:group).permit(:name, :introduction, :image)
      end

      def ensure_correct_user
        @group = Group.find(params[:id])
        unless @group.owner_id == current_user.id
          redirect_to groups_path
        end
      end
    end

#### group_users_controller.rb

    class GroupUsersController < ApplicationController
      before_action :authenticate_user!

      def create
        group_user = current_user.group_users.new(group_id: params[:group_id])
        group_user.save
        redirect_to request.referer
      end

      def destroy
        group_user = current_user.group_users.find_by(group_id: params[:group_id])
        group_user.destroy
        redirect_to request.referer
      end
    end

#### event_notices_controller.rb

    class EventNoticesController < ApplicationController

      def new
        @group = Group.find(params[:group_id])
      end

      def create

        @group = Group.find(params[:group_id])
        @title = params[:title]
        @body = params[:body]

        event = {
          :group => @group,
          :title => @title,
          :body => @body

        }

        EventMailer.send_notifications_to_group(event)

        render :sent
      end

      def sent
        redirect_to group_path(params[:group_id])
      end

    end

## routes

    resources :groups do
      resource :group_users, only: [:create, :destroy]
      resources :event_notices, only: [:new, :create]
      get "event_notices" => "event_notices#sent"
    end

## mailer作成
##### app/mailers/event_mailer.rb

    class EventMailer < ApplicationMailer

      def send_notification(member, event)
        @group = event[:group]
        @title = event[:title]
        @body = event[:body]

        @mail = EventMailer.new()
        mail(
          from: ENV['MAIL_ADDRESS'],
          to:   member.email,
          subject: 'New Event Notice!!'
        )
      end

      def self.send_notifications_to_group(event)
        group = event[:group]
        group.users.each do |member|
          EventMailer.send_notification(member, event).deliver_now
        end
      end

    end

### view
##### event_mailer/send_notification.text.erb

    =========================================
    New event from <%= @group.name %>!!
    ===========================================

    Title:
    <%= @title %>

    Details:
    <%= @body %>

##### event_notices/new.html.erb

    <div class="container">
      <div class="row justify-content-center">
        <div class="col-6">
          <h1>Event Mail</h1>

          <%= form_with url: group_event_notices_path(@group), method: :post, local:true do |f| %>
            <div class="form-group">
              <%= f.label :title %>
              <%= f.text_field :title, class: 'form-control' %>
            </div>
            <div class="form-group">
              <%= f.label :body %>
              <%= f.text_area :body, class: 'form-control' %>
            </div>
            <div class="form-group d-flex justify-content-end">
              <%= f.submit "Send", class: 'btn btn-success' %>
            </div>
          <% end %>
        </div>
      </div>
    </div>

##### event_notices/sent.html.erb

    <div class="container">
      <div class="row justify-content-center">
        <div class="col-6">
          <h1>送信が完了しました</h1>

          <p>内容は以下の通りです。</p>

          <h2>[タイトル]</h2>
          <p><%= @title %></p>


          <h2>[本文]</h2>
          <p><%= @body %></p>
      </div>
    </div>

##### グループオーナーの表示

    <td><%= @group.owner.name ? @group.owner.name : "不明" %></td>
    <td><%= @group.created_at.strftime('%Y/%m/%d') %></td>

# 結果：メールが送れない！！
#       迷惑メール？